lp 是一个非常神奇的指令，你只需要 lp 1.pdf 就可以打印出这个 pdf 文档了。
对于打印这件事情，这是最短路径了。

关于 lp 还有一个可以说的事情是， lp 所属的 CUPS 项目一度是被 Apple 收购了，
所以你可以看到手册中有 Apple Inc. 的字样。

这意味着在 mac 上你也可以使用 lp 指令。

lp 和 lpr 应该是基本等同的，只是从不同的分支发展过来，一个是 unix 老传统，一个是
linux 新名称。

最终放弃 lp 直接打印中文的梦想，看起来很困难。曲线救国，先转换成 pdf ，要什么有什么。

# 如何查看 lp 默认的打印字体

```
sudo grep DefaultFont /etc/cups/ppd/*.ppd
*DefaultFont: Courier
```

# 如何修改 lp 默认的打印字体

> FontPath directory[:...:directoryN]
>     Specifies a colon separated list of directories where fonts can be found.
> On Linux the font-config(1) mechanism is used instead. On macOS the Font Book application manages system-installed fonts.

看起来 lp 依赖的是 font-config 机制，找不到 font-config 但是有 fonts-conf 。

>      Fontconfig contains two essential modules, the configuration module which builds
>      an  internal  configuration from XML files and the matching module which accepts
>      font patterns and returns the nearest matching font.

fonts-conf 是两套机制：
1. 配置模块：根据 xml 配置在内存中生成对应的数据；
2. 匹配模块：输入一个字体模式，返回最接近的字体；

系统级别的配置文件中，没有配置任何的默认字体。

我尝试去 fc-match mono ，得到的是：

> DejaVuSansMono.ttf: "DejaVu Sans Mono" "Regular"

很可能这就是我打印的默认字体。

```
fc-match -a mono | head -n 10

DejaVuSansMono.ttf: "DejaVu Sans Mono" "Regular"
DejaVuSansMono-Bold.ttf: "DejaVu Sans Mono" "Bold"
DejaVuSansMono-Oblique.ttf: "DejaVu Sans Mono" "Oblique"
DejaVuSansMono-BoldOblique.ttf: "DejaVu Sans Mono" "Bold Oblique"
NimbusMonoPS-Regular.otf: "Nimbus Mono PS" "Regular"
NimbusMonoPS-Regular.t1: "Nimbus Mono PS" "Regular"
NimbusMonoPS-Bold.otf: "Nimbus Mono PS" "Bold"
NimbusMonoPS-Bold.t1: "Nimbus Mono PS" "Bold"
NimbusMonoPS-Italic.otf: "Nimbus Mono PS" "Italic"
NimbusMonoPS-Italic.t1: "Nimbus Mono PS" "Italic"
```

目前我在命令行使用 SourceCodePro 看起来效果还不错。

# 修改当前用户的默认配置

```xml
       <?xml version="1.0"?>
       <!DOCTYPE fontconfig SYSTEM "urn:fontconfig:fonts.dtd">
       <!-- $XDG_CONFIG_HOME/fontconfig/fonts.conf for per-user font configuration -->
       <fontconfig>

       <!--
            use WenQuanYi Zen Hei font when serif is requested for Chinese
       -->
       <match>
            <!--
                 If you don't want to use WenQuanYi Zen Hei font for zh-tw etc,
                 you can use zh-cn instead of zh.
                 Please note, even if you set zh-cn, it still matches zh.
                 if you don't like it, you can use compare="eq"
                 instead of compare="contains".
            -->
            <test name="lang" compare="contains">
                 <string>zh</string>
            </test>
            <edit name="family" mode="prepend">
                 <string>SourceCodePro</string>
            </edit>
       </match>
       </fontconfig>
```


# fonts-conf man page

```
FONTS-CONF(5)                                                             FONTS-CONF(5)

NAME
       fonts.conf - Font configuration files

SYNOPSIS
          /etc/fonts/fonts.conf
          /etc/fonts/fonts.dtd
          /etc/fonts/conf.d
          $XDG_CONFIG_HOME/fontconfig/conf.d
          $XDG_CONFIG_HOME/fontconfig/fonts.conf
          ~/.fonts.conf.d
          ~/.fonts.conf

DESCRIPTION
       Fontconfig is a library designed to provide system-wide font configuration, cus‐
       tomization and application access.

FUNCTIONAL OVERVIEW
       Fontconfig contains two essential modules, the configuration module which builds
       an  internal  configuration from XML files and the matching module which accepts
       font patterns and returns the nearest matching font.

   FONT CONFIGURATION
       The configuration module consists of the FcConfig  datatype,  libexpat  and  Fc‐
       ConfigParse  which  walks  over an XML tree and amends a configuration with data
       found within. From an external perspective, configuration of  the  library  con‐
       sists of generating a valid XML tree and feeding that to FcConfigParse. The only
       other mechanism provided to applications for changing the running  configuration
       is to add fonts and directories to the list of application-provided font files.

       The  intent  is  to make font configurations relatively static, and shared by as
       many applications as possible. It is hoped that this will lead  to  more  stable
       font selection when passing names from one application to another.  XML was cho‐
       sen as a configuration file format because it provides a format  which  is  easy
       for external agents to edit while retaining the correct structure and syntax.

       Font  configuration  is  separate from font matching; applications needing to do
       their own matching can access the available fonts from the library  and  perform
       private matching. The intent is to permit applications to pick and choose appro‐
       priate functionality from the library instead of forcing them to choose  between
       this  library  and a private configuration mechanism. The hope is that this will
       ensure that configuration of fonts for all applications can  be  centralized  in
       one place. Centralizing font configuration will simplify and regularize font in‐
       stallation and customization.

   FONT PROPERTIES
       While font patterns may contain essentially any properties, there are some  well
       known properties with associated types. Fontconfig uses some of these properties
       for font matching and font completion. Others are provided as a convenience  for
       the applications' rendering mechanism.

         Property        Type    Description
         --------------------------------------------------------------
         family          String  Font family names
         familylang      String  Languages corresponding to each family
         style           String  Font style. Overrides weight and slant
         stylelang       String  Languages corresponding to each style
         fullname        String  Font full names (often includes style)
         fullnamelang    String  Languages corresponding to each fullname
         slant           Int     Italic, oblique or roman
         weight          Int     Light, medium, demibold, bold or black
         size            Double  Point size
         width           Int     Condensed, normal or expanded
         aspect          Double  Stretches glyphs horizontally before hinting
         pixelsize       Double  Pixel size
         spacing         Int     Proportional, dual-width, monospace or charcell
         foundry         String  Font foundry name
         antialias       Bool    Whether glyphs can be antialiased
         hinting         Bool    Whether the rasterizer should use hinting
         hintstyle       Int     Automatic hinting style
         verticallayout  Bool    Use vertical layout
         autohint        Bool    Use autohinter instead of normal hinter
         globaladvance   Bool    Use font global advance data (deprecated)
         file            String  The filename holding the font
         index           Int     The index of the font within the file
         ftface          FT_Face Use the specified FreeType face object
         rasterizer      String  Which rasterizer is in use (deprecated)
         outline         Bool    Whether the glyphs are outlines
         scalable        Bool    Whether glyphs can be scaled
         color           Bool    Whether any glyphs have color
         scale           Double  Scale factor for point->pixel conversions (deprecated)
         dpi             Double  Target dots per inch
         rgba            Int     unknown, rgb, bgr, vrgb, vbgr,
                                 none - subpixel geometry
         lcdfilter       Int     Type of LCD filter
         minspace        Bool    Eliminate leading from line spacing
         charset         CharSet Unicode chars encoded by the font
         lang            String  List of RFC-3066-style languages this
                                 font supports
         fontversion     Int     Version number of the font
         capability      String  List of layout capabilities in the font
         fontformat      String  String name of the font format
         embolden        Bool    Rasterizer should synthetically embolden the font
         embeddedbitmap  Bool    Use the embedded bitmap instead of the outline
         decorative      Bool    Whether the style is a decorative variant
         fontfeatures    String  List of the feature tags in OpenType to be enabled
         namelang        String  Language name to be used for the default value of
                                 familylang, stylelang, and fullnamelang
         prgname         String  String  Name of the running program
         postscriptname  String  Font family name in PostScript
         fonthashint     Bool    Whether the font has hinting
         order           Int     Order number of the font

   FONT MATCHING
       Fontconfig  performs  matching by measuring the distance from a provided pattern
       to all of the available fonts in the system. The closest matching  font  is  se‐
       lected.  This  ensures  that  a font will always be returned, but doesn't ensure
       that it is anything like the requested pattern.

       Font matching starts with an application constructed pattern.  The  desired  at‐
       tributes  of  the resulting font are collected together in a pattern. Each prop‐
       erty of the pattern can contain one or more values; these are listed in priority
       order; matches earlier in the list are considered "closer" than matches later in
       the list.

       The initial pattern is modified by applying the  list  of  editing  instructions
       specific to patterns found in the configuration; each consists of a match predi‐
       cate and a set of editing operations. They are executed in the  order  they  ap‐
       peared  in the configuration. Each match causes the associated sequence of edit‐
       ing operations to be applied.

       After the pattern has been edited, a sequence of default substitutions are  per‐
       formed to canonicalize the set of available properties; this avoids the need for
       the lower layers to constantly provide default values for various  font  proper‐
       ties during rendering.

       The  canonical font pattern is finally matched against all available fonts.  The
       distance from the pattern to the font is measured for each  of  several  proper‐
       ties:  foundry, charset, family, lang, spacing, pixelsize, style, slant, weight,
       antialias, rasterizer and outline. This list is in priority order -- results  of
       comparing earlier elements of this list weigh more heavily than later elements.

       There  is  one  special case to this rule; family names are split into two bind‐
       ings; strong and weak. Strong family names are given greater precedence  in  the
       match than lang elements while weak family names are given lower precedence than
       lang elements. This permits the document language to drive font  selection  when
       any document specified font is unavailable.

       The  pattern representing that font is augmented to include any properties found
       in the pattern but not found in the font itself; this permits the application to
       pass  rendering  instructions or any other data through the matching system. Fi‐
       nally, the list of editing instructions specific to fonts found in the  configu‐
       ration  are applied to the pattern. This modified pattern is returned to the ap‐
       plication.

       The return value contains sufficient information to  locate  and  rasterize  the
       font,  including  the file name, pixel size and other rendering data. As none of
       the information involved pertains to the FreeType library, applications are free
       to use any rasterization engine or even to take the identified font file and ac‐
       cess it directly.

       The match/edit sequences in the configuration are performed in  two  passes  be‐
       cause  there  are essentially two different operations necessary -- the first is
       to modify how fonts are selected; aliasing  families  and  adding  suitable  de‐
       faults.  The  second  is  to modify how the selected fonts are rasterized. Those
       must apply to the selected font, not the original pattern as false matches  will
       often occur.

   FONT NAMES
       Fontconfig  provides  a textual representation for patterns that the library can
       both accept and generate. The representation is in three parts, first a list  of
       family  names,  second  a  list  of point sizes and finally a list of additional
       properties:

            <families>-<point sizes>:<name1>=<values1>:<name2>=<values2>...

       Values in a list are separated with commas. The name needn't include either fam‐
       ilies  or  point sizes; they can be elided. In addition, there are symbolic con‐
       stants that simultaneously indicate both a name and a value.  Here are some  ex‐
       amples:

         Name                            Meaning
         ----------------------------------------------------------
         Times-12                        12 point Times Roman
         Times-12:bold                   12 point Times Bold
         Courier:italic                  Courier Italic in the default size
         Monospace:matrix=1 .1 0 1       The users preferred monospace font
                                         with artificial obliquing

       The  '\',  '-', ':' and ',' characters in family names must be preceded by a '\'
       character to avoid having them misinterpreted. Similarly, values containing '\',
       '=',  '_',  ':' and ',' must also have them preceded by a '\' character. The '\'
       characters are stripped out of the family name and values as the  font  name  is
       read.

DEBUGGING APPLICATIONS
       To  help  diagnose  font  and  applications problems, fontconfig is built with a
       large amount of internal debugging left enabled. It is controlled  by  means  of
       the  FC_DEBUG environment variable. The value of this variable is interpreted as
       a number, and each bit within that value controls different debugging messages.

         Name         Value    Meaning
         ---------------------------------------------------------
         MATCH            1    Brief information about font matching
         MATCHV           2    Extensive font matching information
         EDIT             4    Monitor match/test/edit execution
         FONTSET          8    Track loading of font information at startup
         CACHE           16    Watch cache files being written
         CACHEV          32    Extensive cache file writing information
         PARSE           64    (no longer in use)
         SCAN           128    Watch font files being scanned to build caches
         SCANV          256    Verbose font file scanning information
         MEMORY         512    Monitor fontconfig memory usage
         CONFIG        1024    Monitor which config files are loaded
         LANGSET       2048    Dump char sets used to construct lang values
         MATCH2        4096    Display font-matching transformation in patterns

       Add the value of the desired debug levels together and assign that (in base  10)
       to the FC_DEBUG environment variable before running the application. Output from
       these statements is sent to stdout.

LANG TAGS
       Each font in the database contains a list of languages it supports. This is com‐
       puted by comparing the Unicode coverage of the font with the orthography of each
       language. Languages are tagged using an RFC-3066 compatible naming and occur  in
       two parts -- the ISO 639 language tag followed a hyphen and then by the ISO 3166
       country code. The hyphen and country code may be elided.

       Fontconfig has orthographies for several languages built into the  library.   No
       provision  has  been made for adding new ones aside from rebuilding the library.
       It currently supports 122 of the 139 languages named in ISO 639-1,  141  of  the
       languages  with  two-letter  codes  from ISO 639-2 and another 30 languages with
       only three-letter codes. Languages with both two and three letter codes are pro‐
       vided with only the two letter code.

       For  languages  used  in multiple territories with radically different character
       sets, fontconfig includes per-territory orthographies.  This  includes  Azerbai‐
       jani, Kurdish, Pashto, Tigrinya and Chinese.

CONFIGURATION FILE FORMAT
       Configuration  files  for fontconfig are stored in XML format; this format makes
       external configuration tools easier to write and ensures that they will generate
       syntactically correct configuration files. As XML files are plain text, they can
       also be manipulated by the expert user using a text editor.

       The  fontconfig  document  type  definition  resides  in  the  external   entity
       "fonts.dtd"; this is normally stored in the default font configuration directory
       (/etc/fonts). Each configuration file should contain the following structure:

            <?xml version="1.0"?>
            <!DOCTYPE fontconfig SYSTEM "urn:fontconfig:fonts.dtd">
            <fontconfig>
       ...
            </fontconfig>

   <FONTCONFIG>
       This is the top level element for a font configuration and  can  contain  <dir>,
       <cachedir>, <include>, <match> and <alias> elements in any order.

   <DIR PREFIX="DEFAULT" SALT="">
       This  element  contains a directory name which will be scanned for font files to
       include in the set of available fonts.

       If 'prefix' is set to "default" or "cwd", the current working directory will  be
       added  as  the  path prefix prior to the value. If 'prefix' is set to "xdg", the
       value in the XDG_DATA_HOME environment variable will be added as the  path  pre‐
       fix.  please  see XDG Base Directory Specification for more details. If 'prefix'
       is set to "relative", the path of current file will be added prior to the value.

       'salt' property affects to determine cache filename. this is useful for  example
       when  having different fonts sets on same path at container and share fonts from
       host on different font path.

   <CACHEDIR PREFIX="DEFAULT">
       This element contains a directory name that is supposed to be stored or read the
       cache  of font information. If multiple elements are specified in the configura‐
       tion file, the directory that can be accessed first in the list will be used  to
       store  the  cache  files. If it starts with '~', it refers to a directory in the
       users  home  directory.  If  'prefix'  is  set  to  "xdg",  the  value  in   the
       XDG_CACHE_HOME environment variable will be added as the path prefix. please see
       XDG Base Directory Specification for more details.   The  default  directory  is
       ``$XDG_CACHE_HOME/fontconfig''  and  it  contains  the cache files named ``<hash
       value>-<architecture>.cache-<version>'', where <version> is the fontconfig cache
       file version number (currently 7).

   <INCLUDE IGNORE_MISSING="NO" PREFIX="DEFAULT">
       This element contains the name of an additional configuration file or directory.
       If a directory, every file within that directory starting with  an  ASCII  digit
       (U+0030  -  U+0039)  and  ending  with the string ``.conf'' will be processed in
       sorted order. When the XML datatype is traversed by FcConfigParse, the  contents
       of  the  file(s) will also be incorporated into the configuration by passing the
       filename(s) to FcConfigLoadAndParse. If 'ignore_missing' is set to "yes" instead
       of  the default "no", a missing file or directory will elicit no warning message
       from the library. If 'prefix' is set to "xdg", the value in the  XDG_CONFIG_HOME
       environment  variable  will be added as the path prefix. please see XDG Base Di‐
       rectory Specification for more details.

   <CONFIG>
       This element provides a place to consolidate additional  configuration  informa‐
       tion. <config> can contain <blank> and <rescan> elements in any order.

   <DESCRIPTION DOMAIN="FONTCONFIG-CONF">
       This  element  is  supposed to hold strings which describe what a config is used
       for.  This string can be translated through gettext. 'domain' needs  to  be  set
       the  proper  name to apply then.  fontconfig will tries to retrieve translations
       with 'domain' from gettext.

   <BLANK>
       Fonts often include "broken" glyphs which appear in the encoding but  are  drawn
       as  blanks on the screen. Within the <blank> element, place each Unicode charac‐
       ters which is supposed to be blank in an <int> element.  Characters  outside  of
       this set which are drawn as blank will be elided from the set of characters sup‐
       ported by the font.

   <REMAP-DIR PREFIX="DEFAULT" AS-PATH="" SALT="">
       This element contains a directory name where will be mapped  as  the  path  'as-
       path'  in  cached information.  This is useful if the directory name is an alias
       (via a bind mount or symlink) to another  directory  in  the  system  for  which
       cached font information is likely to exist.

       'salt' property affects to determine cache filename as same as <dir> element.

   <RESET-DIRS />
       This  element  removes  all  of fonts directories where added by <dir> elements.
       This is useful to override fonts directories from system to own  fonts  directo‐
       ries only.

   <RESCAN>
       The <rescan> element holds an <int> element which indicates the default interval
       between automatic checks for font configuration changes.  Fontconfig will  vali‐
       date  all  of  the configuration files and directories and automatically rebuild
       the internal datastructures when this interval passes.

   <SELECTFONT>
       This element is used to black/white list fonts  from  being  listed  or  matched
       against. It holds acceptfont and rejectfont elements.

   <ACCEPTFONT>
       Fonts matched by an acceptfont element are "whitelisted"; such fonts are explic‐
       itly included in the set of fonts used to resolve list and match  requests;  in‐
       cluding them in this list protects them from being "blacklisted" by a rejectfont
       element. Acceptfont elements include glob and pattern elements which are used to
       match fonts.

   <REJECTFONT>
       Fonts  matched  by  an  rejectfont element are "blacklisted"; such fonts are ex‐
       cluded from the set of fonts used to resolve list and match requests as if  they
       didn't  exist  in  the system. Rejectfont elements include glob and pattern ele‐
       ments which are used to match fonts.

   <GLOB>
       Glob elements hold shell-style filename matching patterns (including  ?  and  *)
       which match fonts based on their complete pathnames. This can be used to exclude
       a set of directories (/usr/share/fonts/uglyfont*), or particular font file types
       (*.pcf.gz), but the latter mechanism relies rather heavily on filenaming conven‐
       tions which can't be relied upon. Note that globs only apply to directories, not
       to individual fonts.

   <PATTERN>
       Pattern  elements  perform  list-style matching on incoming fonts; that is, they
       hold a list of elements and associated values. If all of those elements  have  a
       matching  value,  then  the pattern matches the font. This can be used to select
       fonts based on attributes of the font (scalable, bold, etc), which is a more re‐
       liable  mechanism  than  using file extensions.  Pattern elements include patelt
       elements.

   <PATELT NAME="PROPERTY">
       Patelt elements hold a single pattern element and list of values. They must have
       a 'name' attribute which indicates the pattern element name. Patelt elements in‐
       clude int, double, string, matrix, bool, charset and const elements.

   <MATCH TARGET="PATTERN">
       This element holds first a (possibly empty) list of <test> elements and  then  a
       (possibly  empty) list of <edit> elements. Patterns which match all of the tests
       are subjected to all the edits. If 'target' is set to "font" instead of the  de‐
       fault  "pattern",  then  this  element applies to the font name resulting from a
       match rather than a font pattern to be matched. If 'target' is  set  to  "scan",
       then this element applies when the font is scanned to build the fontconfig data‐
       base.

   <TEST QUAL="ANY" NAME="PROPERTY" TARGET="DEFAULT" COMPARE="EQ">
       This element contains a single value which is compared with  the  target  ('pat‐
       tern',  'font',  'scan' or 'default') property "property" (substitute any of the
       property names seen above). 'compare' can be  one  of  "eq",  "not_eq",  "less",
       "less_eq", "more", "more_eq", "contains" or "not_contains". 'qual' may either be
       the default, "any", in which case the match succeeds  if  any  value  associated
       with  the  property  matches  the test value, or "all", in which case all of the
       values associated with the property must match the test  value.  'ignore-blanks'
       takes  a  boolean  value.  if  'ignore-blanks'  is set "true", any blanks in the
       string will be ignored on its comparison. this  takes  effects  only  when  com‐
       pare="eq"  or  compare="not_eq".   When used in a <match target="font"> element,
       the target= attribute in the <test> element selects between matching the  origi‐
       nal  pattern  or  the font. "default" selects whichever target the outer <match>
       element has selected.

   <EDIT NAME="PROPERTY" MODE="ASSIGN" BINDING="WEAK">
       This element contains a list of expression elements (any of the value or  opera‐
       tor  elements). The expression elements are evaluated at run-time and modify the
       property "property". The modification depends on whether "property" was  matched
       by one of the associated <test> elements, if so, the modification may affect the
       first matched value. Any values inserted into the property are given  the  indi‐
       cated  binding  ("strong", "weak" or "same") with "same" binding using the value
       from the matched pattern element.  'mode' is one of:

         Mode                    With Match              Without Match
         ---------------------------------------------------------------------
         "assign"                Replace matching value  Replace all values
         "assign_replace"        Replace all values      Replace all values
         "prepend"               Insert before matching  Insert at head of list
         "prepend_first"         Insert at head of list  Insert at head of list
         "append"                Append after matching   Append at end of list
         "append_last"           Append at end of list   Append at end of list
         "delete"                Delete matching value   Delete all values
         "delete_all"            Delete all values       Delete all values

   <INT>, <DOUBLE>, <STRING>, <BOOL>
       These elements hold a single value of the indicated type. <bool>  elements  hold
       either  true or false. An important limitation exists in the parsing of floating
       point numbers -- fontconfig requires that the mantissa start with a digit, not a
       decimal  point,  so insert a leading zero for purely fractional values (e.g. use
       0.5 instead of .5 and -0.5 instead of -.5).

   <MATRIX>
       This element holds four numerical expressions of an affine  transformation.   At
       their  simplest  these  will be four <double> elements but they can also be more
       involved expressions.

   <RANGE>
       This element holds the two <int> elements of a range representation.

   <CHARSET>
       This element holds at least one <int> element of an Unicode code point or more.

   <LANGSET>
       This element holds at least one <string> element of a  RFC-3066-style  languages
       or more.

   <NAME>
       Holds  a  property  name.  Evaluates to the first value from the property of the
       pattern. If the 'target' attribute is not present, it will default to 'default',
       in  which  case  the  property  is  returned from the font pattern during a tar‐
       get="font" match, and to the pattern during a target="pattern" match. The attri‐
       bute  can  also  take  the values 'font' or 'pattern' to explicitly choose which
       pattern to use. It is an error to use a target of 'font' in  a  match  that  has
       target="pattern".

   <CONST>
       Holds  the  name  of a constant; these are always integers and serve as symbolic
       names for common font values:

         Constant        Property        Value
         -------------------------------------
         thin            weight          0
         extralight      weight          40
         ultralight      weight          40
         light           weight          50
         demilight       weight          55
         semilight       weight          55
         book            weight          75
         regular         weight          80
         normal          weight          80
         medium          weight          100
         demibold        weight          180
         semibold        weight          180
         bold            weight          200
         extrabold       weight          205
         black           weight          210
         heavy           weight          210
         roman           slant           0
         italic          slant           100
         oblique         slant           110
         ultracondensed  width           50
         extracondensed  width           63
         condensed       width           75
         semicondensed   width           87
         normal          width           100
         semiexpanded    width           113
         expanded        width           125
         extraexpanded   width           150
         ultraexpanded   width           200
         proportional    spacing         0
         dual            spacing         90
         mono            spacing         100
         charcell        spacing         110
         unknown         rgba            0
         rgb             rgba            1
         bgr             rgba            2
         vrgb            rgba            3
         vbgr            rgba            4
         none            rgba            5
         lcdnone         lcdfilter       0
         lcddefault      lcdfilter       1
         lcdlight        lcdfilter       2
         lcdlegacy       lcdfilter       3
         hintnone        hintstyle       0
         hintslight      hintstyle       1
         hintmedium      hintstyle       2
         hintfull        hintstyle       3

   <OR>, <AND>, <PLUS>, <MINUS>, <TIMES>, <DIVIDE>
       These elements perform the specified operation on a list of expression elements.
       <or> and <and> are boolean, not bitwise.

   <EQ>, <NOT_EQ>, <LESS>, <LESS_EQ>, <MORE>, <MORE_EQ>, <CONTAINS>, <NOT_CONTAINS
       These elements compare two values, producing a boolean result.

   <NOT>
       Inverts the boolean sense of its one expression element

   <IF>
       This element takes three expression elements; if the value of the first is true,
       it produces the value of the second, otherwise it  produces  the  value  of  the
       third.

   <ALIAS>
       Alias  elements  provide a shorthand notation for the set of common match opera‐
       tions needed to substitute one font family for another. They contain a  <family>
       element  followed  by  optional <prefer>, <accept> and <default> elements. Fonts
       matching the <family> element are edited to prepend the list of <prefer>ed fami‐
       lies  before  the  matching <family>, append the <accept>able families after the
       matching <family> and append the <default> families to the  end  of  the  family
       list.

   <FAMILY>
       Holds a single font family name

   <PREFER>, <ACCEPT>, <DEFAULT>
       These hold a list of <family> elements to be used by the <alias> element.

EXAMPLE CONFIGURATION FILE
   SYSTEM CONFIGURATION FILE
       This is an example of a system-wide configuration file

       <?xml version="1.0"?>
       <!DOCTYPE fontconfig SYSTEM "urn:fontconfig:fonts.dtd">
       <!-- /etc/fonts/fonts.conf file to configure system font access -->
       <fontconfig>
       <!--
            Find fonts in these directories
       -->
       <dir>/usr/share/fonts</dir>
       <dir>/usr/X11R6/lib/X11/fonts</dir>

       <!--
            Accept deprecated 'mono' alias, replacing it with 'monospace'
       -->
       <match target="pattern">
            <test qual="any" name="family"><string>mono</string></test>
            <edit name="family" mode="assign"><string>monospace</string></edit>
       </match>

       <!--
            Names not including any well known alias are given 'sans-serif'
       -->
       <match target="pattern">
            <test qual="all" name="family" compare="not_eq"><string>sans-serif</string></test>
            <test qual="all" name="family" compare="not_eq"><string>serif</string></test>
            <test qual="all" name="family" compare="not_eq"><string>monospace</string></test>
            <edit name="family" mode="append_last"><string>sans-serif</string></edit>
       </match>

       <!--
            Load per-user customization file, but don't complain
            if it doesn't exist
       -->
       <include ignore_missing="yes" prefix="xdg">fontconfig/fonts.conf</include>

       <!--
            Load local customization files, but don't complain
            if there aren't any
       -->
       <include ignore_missing="yes">conf.d</include>
       <include ignore_missing="yes">local.conf</include>

       <!--
            Alias well known font names to available TrueType fonts.
            These substitute TrueType faces for similar Type1
            faces to improve screen appearance.
       -->
       <alias>
            <family>Times</family>
            <prefer><family>Times New Roman</family></prefer>
            <default><family>serif</family></default>
       </alias>
       <alias>
            <family>Helvetica</family>
            <prefer><family>Arial</family></prefer>
            <default><family>sans</family></default>
       </alias>
       <alias>
            <family>Courier</family>
            <prefer><family>Courier New</family></prefer>
            <default><family>monospace</family></default>
       </alias>

       <!--
            Provide required aliases for standard names
            Do these after the users configuration file so that
            any aliases there are used preferentially
       -->
       <alias>
            <family>serif</family>
            <prefer><family>Times New Roman</family></prefer>
       </alias>
       <alias>
            <family>sans</family>
            <prefer><family>Arial</family></prefer>
       </alias>
       <alias>
            <family>monospace</family>
            <prefer><family>Andale Mono</family></prefer>
       </alias>

       <--
            The example of the requirements of OR operator;
            If the 'family' contains 'Courier New' OR 'Courier'
            add 'monospace' as the alternative
       -->
       <match target="pattern">
            <test name="family" compare="eq">
                 <string>Courier New</string>
            </test>
            <edit name="family" mode="prepend">
                 <string>monospace</string>
            </edit>
       </match>
       <match target="pattern">
            <test name="family" compare="eq">
                 <string>Courier</string>
            </test>
            <edit name="family" mode="prepend">
                 <string>monospace</string>
            </edit>
       </match>

       </fontconfig>

   USER CONFIGURATION FILE
       This  is  an  example  of  a per-user configuration file that lives in $XDG_CON‐
       FIG_HOME/fontconfig/fonts.conf

       <?xml version="1.0"?>
       <!DOCTYPE fontconfig SYSTEM "urn:fontconfig:fonts.dtd">
       <!-- $XDG_CONFIG_HOME/fontconfig/fonts.conf for per-user font configuration -->
       <fontconfig>

       <!--
            Private font directory
       -->
       <dir prefix="xdg">fonts</dir>

       <!--
            use rgb sub-pixel ordering to improve glyph appearance on
            LCD screens.  Changes affecting rendering, but not matching
            should always use target="font".
       -->
       <match target="font">
            <edit name="rgba" mode="assign"><const>rgb</const></edit>
       </match>
       <!--
            use WenQuanYi Zen Hei font when serif is requested for Chinese
       -->
       <match>
            <!--
                 If you don't want to use WenQuanYi Zen Hei font for zh-tw etc,
                 you can use zh-cn instead of zh.
                 Please note, even if you set zh-cn, it still matches zh.
                 if you don't like it, you can use compare="eq"
                 instead of compare="contains".
            -->
            <test name="lang" compare="contains">
                 <string>zh</string>
            </test>
            <test name="family">
                 <string>serif</string>
            </test>
            <edit name="family" mode="prepend">
                 <string>WenQuanYi Zen Hei</string>
            </edit>
       </match>
       <!--
            use VL Gothic font when sans-serif is requested for Japanese
       -->
       <match>
            <test name="lang" compare="contains">
                 <string>ja</string>
            </test>
            <test name="family">
                 <string>sans-serif</string>
            </test>
            <edit name="family" mode="prepend">
                 <string>VL Gothic</string>
            </edit>
       </match>
       </fontconfig>

FILES
       fonts.conf contains configuration information for the  fontconfig  library  con‐
       sisting  of  directories to look at for font information as well as instructions
       on editing program specified font patterns before attempting to match the avail‐
       able fonts. It is in XML format.

       conf.d  is  the  conventional  name  for a directory of additional configuration
       files managed by external applications or the local administrator. The filenames
       starting with decimal digits are sorted in lexicographic order and used as addi‐
       tional configuration files. All of these files are in  XML  format.  The  master
       fonts.conf file references this directory in an <include> directive.

       fonts.dtd is a DTD that describes the format of the configuration files.

       $XDG_CONFIG_HOME/fontconfig/conf.d  and ~/.fonts.conf.d is the conventional name
       for a per-user directory of (typically auto-generated) configuration files,  al‐
       though  the  actual  location is specified in the global fonts.conf file. please
       note that ~/.fonts.conf.d is deprecated now. it will not be read by  default  in
       the future version.

       $XDG_CONFIG_HOME/fontconfig/fonts.conf and ~/.fonts.conf is the conventional lo‐
       cation for per-user font configuration, although the actual location  is  speci‐
       fied in the global fonts.conf file. please note that ~/.fonts.conf is deprecated
       now. it will not be read by default in the future version.

       $XDG_CACHE_HOME/fontconfig/*.cache-* and  ~/.fontconfig/*.cache-* is the conven‐
       tional  repository  of  font  information  that isn't found in the per-directory
       caches. This file is automatically maintained by fontconfig.  please  note  that
       ~/.fontconfig/*.cache-* is deprecated now. it will not be read by default in the
       future version.

ENVIRONMENT VARIABLES
       FONTCONFIG_FILE is used to override the default configuration file.

       FONTCONFIG_PATH is used to override the default configuration directory.

       FONTCONFIG_SYSROOT is used to set a default sysroot directory.

       FC_DEBUG is used to output the detailed debugging messages. see Debugging Appli‐
       cations section for more details.

       FC_DBG_MATCH_FILTER is used to filter out the patterns. this takes a comma-sepa‐
       rated list of object names and effects only when FC_DEBUG has MATCH2. see Debug‐
       ging Applications section for more details.

       FC_LANG  is  used  to  specify  the  default language as the weak binding in the
       query. if this isn't set, the default language will be determined  from  current
       locale.

       FONTCONFIG_USE_MMAP is used to control the use of mmap(2) for the cache files if
       available. this take a boolean value. fontconfig will checks if the cache  files
       are  stored  on  the  filesystem that is safe to use mmap(2). explicitly setting
       this environment variable will causes skipping this check and enforce to use  or
       not use mmap(2) anyway.

       SOURCE_DATE_EPOCH is used to ensure fc-cache(1) generates files in a determinis‐
       tic manner in order to support reproducible builds. When set to a numeric repre‐
       sentation  of  UNIX  timestamp, fontconfig will prefer this value over using the
       modification timestamps of the input files in  order  to  identify  which  cache
       files  require  regeneration.  If SOURCE_DATE_EPOCH is not set (or is newer than
       the mtime of the directory), the existing behaviour is unchanged.

SEE ALSO
       fc-cat(1), fc-cache(1), fc-list(1), fc-match(1), fc-query(1),  SOURCE_DATE_EPOCH
       <URL:https://reproducible-builds.org/specs/source-date-epoch/>.

VERSION
       Fontconfig version 2.13.94

                                      29 6月 2021                         FONTS-CONF(5)
```

# 当前的默认配置 xml 内容

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "urn:fontconfig:fonts.dtd">
<!-- /etc/fonts/fonts.conf file to configure system font access -->
<fontconfig>
	<description>Default configuration file</description>

<!--
	DO NOT EDIT THIS FILE.
	IT WILL BE REPLACED WHEN FONTCONFIG IS UPDATED.
	LOCAL CHANGES BELONG IN 'local.conf'.

	The intent of this standard configuration file is to be adequate for
	most environments.  If you have a reasonably normal environment and
	have found problems with this configuration, they are probably
	things that others will also want fixed.  Please submit any
	problems to the fontconfig bugzilla system located at fontconfig.org

	Note that the normal 'make install' procedure for fontconfig is to
	replace any existing fonts.conf file with the new version.  Place
	any local customizations in local.conf which this file references.

	Keith Packard
-->

<!-- Font directory list -->

	<dir>/usr/share/fonts</dir>

	<dir>/usr/share/X11/fonts/Type1</dir> <dir>/usr/share/X11/fonts/TTF</dir> <dir>/usr/local/share/fonts</dir>
	<dir prefix="xdg">fonts</dir>
	<!-- the following element will be removed in the future -->
	<dir>~/.fonts</dir>

<!--
  Accept deprecated 'mono' alias, replacing it with 'monospace'
-->
	<match target="pattern">
		<test qual="any" name="family">
			<string>mono</string>
		</test>
		<edit name="family" mode="assign" binding="same">
			<string>monospace</string>
		</edit>
	</match>

<!--
  Accept alternate 'sans serif' spelling, replacing it with 'sans-serif'
-->
	<match target="pattern">
		<test qual="any" name="family">
			<string>sans serif</string>
		</test>
		<edit name="family" mode="assign" binding="same">
			<string>sans-serif</string>
		</edit>
	</match>

<!--
  Accept deprecated 'sans' alias, replacing it with 'sans-serif'
-->
	<match target="pattern">
		<test qual="any" name="family">
			<string>sans</string>
		</test>
		<edit name="family" mode="assign" binding="same">
			<string>sans-serif</string>
		</edit>
	</match>
<!--
  Accept alternate 'system ui' spelling, replacing it with 'system-ui'
-->
	<match target="pattern">
		<test qual="any" name="family">
			<string>system ui</string>
		</test>
		<edit name="family" mode="assign" binding="same">
			<string>system-ui</string>
		</edit>
	</match>

<!--
  Load local system customization file
-->
	<include ignore_missing="yes">conf.d</include>

<!-- Font cache directory list -->

	<cachedir>/usr/lib/fontconfig/cache</cachedir>
	<cachedir prefix="xdg">fontconfig</cachedir>
	<!-- the following element will be removed in the future -->
	<cachedir>~/.fontconfig</cachedir>

	<config>
<!--
  Rescan configuration every 30 seconds when FcFontSetList is called
 -->
		<rescan>
			<int>30</int>
		</rescan>
	</config>

</fontconfig>
```

# fc-list

```
FC-LIST(1)                                                                                                                FC-LIST(1)

NAME
       fc-list - list available fonts

SYNOPSIS
       fc-list [ -vVh ]  [ --verbose ]  [  [ -f format ]  [ --format format ]  ]  [  [ -q ]  [ --quiet ]  ]  [ --version ]  [ --help
       ]

        [ pattern  [ element... ]   ]

DESCRIPTION
       fc-list lists fonts and styles available on the system for applications using fontconfig.  If  any  elements  are  specified,
       only those are printed.  Otherwise family and style are printed, unless verbose output is requested.

OPTIONS
       This  program  follows  the usual GNU command line syntax, with long options starting with two dashes (`-'). A summary of op‐
       tions is included below.

       -v     Print verbose output of the whole font pattern for each match, or elements if any is provided.

       -f     Format output according to the format specifier format.

       -q     Suppress all normal output. returns 1 as the error code if no fonts matched.

       -V     Show version of the program and exit.

       -h     Show summary of options.

       pattern
              If this argument is set, only fonts matching pattern are displayed.

       element
              If set, the element property is displayed for matching fonts.

EXAMPLES
       fc-list
              Lists all font faces.

       fc-list :lang=hi
              Lists font faces that cover Hindi.

       fc-list : family style file spacing
              Lists the filename and spacing value for each font face. ``:'' is an empty pattern that matches all fonts.

SEE ALSO
       fc-match(1) FcFontList(3) FcPatternFormat(3) fc-cat(1) fc-cache(1) fc-pattern(1) fc-query(1) fc-scan(1)

       The fontconfig user's guide, in HTML format: /usr/share/doc/fontconfig/fontconfig-user.html.

AUTHOR
       This manual page was written by Keith Packard <keithp@keithp.com> and Josselin Mouette <joss@debian.org>.

                                                            Aug 13, 2008                                                  FC-LIST(1)
```

# fc-match

```
FC-MATCH(1)                                                                                                              FC-MATCH(1)

NAME
       fc-match - match available fonts

SYNOPSIS
       fc-match  [ -asvVh ]  [ --all ]  [ --sort ]  [ --verbose ]  [  [ -f format ]  [ --format format ]  ]  [ --version ]  [ --help
       ]

        [ pattern  [ element... ]   ]

DESCRIPTION
       fc-match matches pattern (empty pattern by default) using the normal fontconfig matching rules to find the best  font  avail‐
       able.  If  --sort  is  given, the sorted list of best matching fonts is displayed.  The --all option works like --sort except
       that no pruning is done on the list of fonts.

       If any elements are specified, only those are printed.  Otherwise short file name, family, and style are printed, unless ver‐
       bose output is requested.

OPTIONS
       This  program  follows  the usual GNU command line syntax, with long options starting with two dashes (`-'). A summary of op‐
       tions is included below.

       -a     Displays sorted list of best matching fonts, but do not do any pruning on the list.

       -s     Displays sorted list of best matching fonts.

       -v     Print verbose output of the whole font pattern for each match, or elements if any is provided.

       -f     Format output according to the format specifier format.

       -V     Show version of the program and exit.

       -h     Show summary of options.

       pattern
              Displays fonts matching pattern (uses empty pattern by default).

       element
              If set, the element property is displayed for matching fonts.

EXAMPLES
       fc-match sans
              Display the best matching font categorized into sans-serif generic family, filtered by current locale

       fc-match sans:lang=en
              Display the best matching font categorized into sans-serif generic family, filtered by English language

       fc-match sans:lang=en:weight=bold
              Display the best matching font categorized into sans-serif generic family, filtered by English language and weight  is
              bold.

SEE ALSO
       fc-list(1) FcFontMatch(3) FcFontSort(3) FcPatternFormat(3) fc-cat(1) fc-cache(1) fc-pattern(1) fc-query(1) fc-scan(1)

       The fontconfig user's guide, in HTML format: /usr/share/doc/fontconfig/fontconfig-user.html.

AUTHOR
       This manual page was updated by Patrick Lam <plam@csail.mit.edu>.

                                                            Aug 13, 2008                                                 FC-MATCH(1)
```

```
FC-CACHE(1)                                                                                                              FC-CACHE(1)

NAME
       fc-cache - build font information cache files

SYNOPSIS
       fc-cache  [  -EfrsvVh  ]   [  --error-on-no-fonts  ]  [ --force ]  [ --really-force ]  [  [ -y dir ]  [ --sysroot dir ]  ]  [
       --system-only ]  [ --verbose ]  [ --version ]  [ --help ]  [ dir... ]

DESCRIPTION
       fc-cache scans the font directories on the system and builds font information cache files for applications  using  fontconfig
       for their font handling.

       If  directory  arguments  are  not  given,  fc-cache uses each directory in the current font configuration. Each directory is
       scanned for font files readable by FreeType. A cache is created which contains properties of each  font  and  the  associated
       filename.  This cache is used to speed up application startup when using the fontconfig library.

       Note that fc-cache must be executed once per architecture to generate font information customized for that architecture.

OPTIONS
       This  program  follows  the usual GNU command line syntax, with long options starting with two dashes (`-'). A summary of op‐
       tions is included below.

       -E     Raise an error if there are no fonts in dir or directories in the configuration if not given.

       -f     Force re-generation of apparently up-to-date cache files, overriding the timestamp checking.

       -r     Erase all existing cache files and rescan.

       -s     Only scan system-wide directories, omitting the places located in the user's home directory.

       -v     Display status information while busy.

       -y     Prepend dir to all paths for scanning.

       -h     Show summary of options.

       -V     Show version of the program and exit.

       dir    Directory to scan for fonts.

RETURN CODES
       fc-cache returns zero if the caches successfully generated. otherwise non-zero.

FILES
       %cachedir%/*-%arch%.cache-%version%
              These files are generated by fc-cache and contain maps from file names to font properties. They are read by the  font‐
              config library at application startup to locate appropriate fonts.

SEE ALSO
       fc-cat(1) fc-list(1) fc-match(1) fc-pattern(1) fc-query(1) fc-scan(1)

       The fontconfig user's guide, in HTML format: /usr/share/doc/fontconfig/fontconfig-user.html.

AUTHOR
       This manual page was written by Keith Packard <keithp@keithp.com> and Josselin Mouette <joss@debian.org>.

                                                            Aug 13, 2008                                                 FC-CACHE(1)
```

# lp man page

```
lp(1)                                  Apple Inc.                                 lp(1)

NAME
       lp - print files

SYNOPSIS
       lp  [  -E  ]  [  -U  username  ] [ -c ] [ -d destination[/instance] ] [ -h host‐
       name[:port] ] [ -m ] [ -n num-copies ] [ -o option[=value] ] [ -q priority  ]  [
       -s ] [ -t title ] [ -H handling ] [ -P page-list ] [ -- ] [ file(s) ]
       lp  [ -E ] [ -U username ] [ -c ] [ -h hostname[:port] ] [ -i job-id ] [ -n num-
       copies ] [ -o option[=value] ] [ -q priority ] [ -t title ] [ -H handling ] [ -P
       page-list ]

DESCRIPTION
       lp submits files for printing or alters a pending job.  Use a filename of "-" to
       force printing from the standard input.

   THE DEFAULT DESTINATION
       CUPS provides many ways to set the default destination. The LPDEST  and  PRINTER
       environment  variables are consulted first.  If neither are set, the current de‐
       fault set using the lpoptions(1) command is used, followed by  the  default  set
       using the lpadmin(8) command.

OPTIONS
       The following options are recognized by lp:

       --   Marks the end of options; use this to print a file whose name begins with a
            dash (-).

       -E   Forces encryption when connecting to the server.

       -U username
            Specifies the username to use when connecting to the server.

       -c   This option is provided for backwards-compatibility only. On  systems  that
            support it, this option forces the print file to be copied to the spool di‐
            rectory before printing.  In CUPS, print  files  are  always  sent  to  the
            scheduler via IPP which has the same effect.

       -d destination
            Prints files to the named printer.

       -h hostname[:port]
            Chooses an alternate server.

       -i job-id
            Specifies an existing job to modify.

       -m   Sends an email when the job is completed.

       -n copies
            Sets the number of copies to print.

       -o "name=value [ ... name=value ]"
            Sets one or more job options.  See "COMMON JOB OPTIONS" below.

       -q priority
            Sets the job priority from 1 (lowest) to 100 (highest).  The default prior‐
            ity is 50.

       -s   Do not report the resulting job IDs (silent mode.)

       -t "name"
            Sets the job name.

       -H hh:mm

       -H hold

       -H immediate

       -H restart

       -H resume
            Specifies when the job should be printed.  A value of immediate will  print
            the file immediately, a value of hold will hold the job indefinitely, and a
            UTC time value (HH:MM) will hold the job until the specified UTC  (not  lo‐
            cal)  time.  Use a value of resume with the -i option to resume a held job.
            Use a value of restart with the -i option to restart a completed job.

       -P page-list
            Specifies which pages to print in the document.  The  list  can  contain  a
            list  of  numbers  and  ranges (#-#) separated by commas, e.g., "1,3-5,16".
            The page numbers refer to the output pages and not the document's  original
            pages - options like "number-up" can affect the numbering of the pages.

   COMMON JOB OPTIONS
       Aside  from  the  printer-specific options reported by the lpoptions(1) command,
       the following generic options are available:

       -o job-sheets=name
            Prints a cover page (banner) with the document.  The "name" can be "classi‐
            fied",  "confidential",  "secret",  "standard",  "topsecret", or "unclassi‐
            fied".

       -o media=size
            Sets the page size to size. Most printers support at least the  size  names
            "a4", "letter", and "legal".

       -o number-up={2|4|6|9|16}
            Prints 2, 4, 6, 9, or 16 document (input) pages on each output page.

       -o orientation-requested=4
            Prints the job in landscape (rotated 90 degrees counter-clockwise).

       -o orientation-requested=5
            Prints the job in landscape (rotated 90 degrees clockwise).

       -o orientation-requested=6
            Prints the job in reverse portrait (rotated 180 degrees).

       -o print-quality=3

       -o print-quality=4

       -o print-quality=5
            Specifies the output quality - draft (3), normal (4), or best (5).

       -o sides=one-sided
            Prints on one side of the paper.

       -o sides=two-sided-long-edge
            Prints on both sides of the paper for portrait output.

       -o sides=two-sided-short-edge
            Prints on both sides of the paper for landscape output.

CONFORMING TO
       Unlike  the  System  V printing system, CUPS allows printer names to contain any
       printable character except SPACE, TAB, "/", or "#".   Also,  printer  and  class
       names are not case-sensitive.

       The  -q  option accepts a different range of values than the Solaris lp command,
       matching the IPP job priority values (1-100, 100 is highest priority) instead of
       the Solaris values (0-39, 0 is highest priority).

EXAMPLES
       Print two copies of a document to the default printer:

           lp -n 2 filename

       Print a double-sided legal document to a printer called "foo":

           lp -d foo -o media=legal -o sides=two-sided-long-edge filename

       Print a presentation document 2-up to a printer called "bar":

           lp -d bar -o number-up=2 filename

SEE ALSO
       cancel(1),  lpadmin(8),  lpoptions(1),  lpq(1), lpr(1), lprm(1), lpstat(1), CUPS
       Online Help (http://localhost:631/help)

COPYRIGHT
       Copyright © 2007-2019 by Apple Inc.

26 April 2019                             CUPS                                    lp(1)
```
