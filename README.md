# vPhon: a Vietnamese phonetizer

Package: vPhon version 2.0.1

Author: James Kirby <j.kirby@ed.ac.uk>

Web: https://github.com/kirbyj/vPhon

This software takes UTF-8 Vietnamese orthography and returns broad transcription UTF-8 output in the International Phonetic Association (IPA) alphabet for three major dialects of Vietnamese: Northern (Hà Nội), Central (Huế), and Southern (Sài Gòn) speech.

For more information on the IPA, go to

https://www.internationalphoneticassociation.org/content/full-ipa-chart

## Representational preliminaries

Note that an IPA representation can take one of two alternative starting points:

- a phonemic representation that clarifies minimal contrasts,
- a phonetic representation that reflects pronunciation details.

This implementation currently uses the first approach, meaning that many non-contrastive details are not represented. However, pronunciations where the citation forms differ from dialect to dialect are given, e.g. *anh* > [ɛŋ] (Northern), [an] (Southern).


### Onsets

The choice of onset symbols for the default (Northern) dialect follows Kirby (2011). Following Thompson (1965) and others, vPhon uses different onset symbols for *tr* and *ch*, *x* and *s*, and *r* in Central and Southern dialects, with the understanding that the phonetic realizations of these graphemes varies. For example, for many Southern speakers *tr* and *ch* spellings may be merged towards [c] or [tɕ], and *x* and *s* towards [s]. Users could easily modify the `Rules` files to represent such mergers. In addition, while vPhon uses [ʐ] to encode orthographic *r* in Central and Southern dialects, the actual phonetic realization can vary widely, unlike in typical Hanoi speech where *r* is consistently [z]. The use of distinct symbols for the different dialects is designed to facilitate cross-dialectal comparison, but not to make specific claims about phonetic realization or variability.

### Vowels

Segmental correspondences generally follow Thompson (1965: 98-103) and Cao Xuân Hạo (1997: 126). vPhon assumes 11 nuclei (following Nguyễn Đình-Hoà 1997, cf. Cao Xuân Hạo 1998: 96-102) and 3 diphthongs /iə uə ɯə/; this decision was made to try and remain as agnostic as possible regarding the actual phonetic values of these segments. Monopthongization of labial-final diphthongs is represented for the Southern dialects (Thompson 1965: 86; Hoàng Thị Châu 1989: 174 *ff*.).

Length is not explicitly represented excepting the phonemic length between the vowel pairs *â/ơ* and *ă/a*. The long variants are not marked with a diacritic; the short vowels *â* and *ă* are transcribed as /ɤ̆/ and /ă/, respectively. Orthographic *ô*, *ôô* are both transcribed as /o/; similarly, *o, oo* are both transcribed as /ɔ/.

### Finals

By default, vPhon does not recognize final palatal segments /c ɲ/, as their values may be predicted from the preceding vocalic segments.  However, the `-p, --palatals` flag causes palatal [c ɲ] codas to be output (Hoàng Thị Châu 1989: 172 *ff*.; cf. Cao Xuân Hạo 1998: 88-102).

Final labialized allophones of /ŋ k/ are represented as [ŋ͡m k͡p].

### Tones

vPhon represents tone using one of two methods. By default, vPhon will return Chao tone numbers in the table below, which are based on Alves (2007a), Hoàng Thị Châu (1989), Nguyễn Văn Lợi and Edmonson (1997), Nguyễn Văn Lợi (2012), and Vũ (1982). Note that, especially for Central (Huế) speech, a wide range of proposals abound: see Nguyễn Văn Lợi (2012) for some discussion. *g* is used to indiciate both (medial) laryngealization and (final) glottalization.

Name | North | Central | South
---- | ----- | ------- | -----
ngang|33|45|33
sắc|24/45|2g4/45|45
huyền|21|42|21
hỏi|312|32g|214
ngã|35g|32g|214
nặng|21/g|31|212

vPhon also provides an option (given the `-6` flag) to return integer values for tones given in e.g. Phạm (2001, 59):

| Name  | Number |
| ----- | --- |
| ngang | 1   |
| huyền | 2   |
| ngã   | 3   |
| hỏi   | 4   |
| sắc   | 5   |
| nặng  | 6   |

If passed the `-8` flag, *sắc* and *nặng* tones in closed syllables are returned as 7 and 8, respectively. These were returned as 5b and 6b in vPhon v.1.0.0 and earlier.

Note that for the Central and Southern dialects, the relationship of tone to number is slightly different. Orthographic *hỏi* and *ngã* tones are both phonetized as 4 when vPhon is passed the `-6` or `-8` flags, representing the (phonological) mergers present in those dialects (Hoàng 1989: 212 *ff.*)

## Installation

No installation is required. You must have a working version of Python 3 installed and in your path. We have tested only using 3.7.1. vPhon requires the `sys`, `string`, `re`, `io`, and `argparse` modules, all of which should come standard with Python >= 3.5.

If you need to use vPhon with Python 2, see [v1.0.0](https://github.com/kirbyj/vPhon/releases/tag/v1.0).

## Usage

From v2.0.0, vPhon does not take any obligatory arguments. If a `-d, --dialect` option is not specified, it defaults to using the standard (Northern) dialect correspondence set.  The correspondence files may be found in the `Rules/` directory.

vPhon was designed to work in a manner similar to that of Unix command line utilities, and as such it expects to receive UTF-8 text via STDIN. If you have a file called `tuoi.txt`, for example, and want to create Southern-dialect IPA from it, either of the following will work:

```
[user@terminal]$ python vPhon.py -d S < tuoi.txt
[user@terminal]$ cat tuoi.txt | python vPhon.py --dialect s
```

If no input is provided, vPhon will enter an interactive mode allowing you to enter UTF-8 Vietnamese orthography on the command line. When you are done, send `EOF` (Ctrl-D) to get the output. By default, output is sent to STDOUT.

## Options

The full list of options can be seen by using the `-h, --help` flag:

```
[user@terminal]$ python vPhon.py --help
usage: vPhon.py [-h] [-d {n,c,s}] [-g] [-6] [-8] [-p] [-t] [-o OUTPUT_ORTHO]
                [-m DELIMIT]

python vPhon.py <input>

optional arguments:
  -h, --help            show this help message and exit
  -d {n,c,s}, --dialect {n,c,s}
                        specify dialect region (Northern, Central, Southern)
  -g, --glottal         prepend glottal stops to onsetless syllables
  -6, --pham            phonetize tones as 1-6
  -8, --cao             phonetize tones as 1-8
  -p, --palatals        use word-final palatal velars in Northern dialects
  -t, --tokenize        preserve underscores or hyphens in tokenized inputs
                        (e.g., anh_ta = anh1_ta1)
  -o OUTPUT_ORTHO, --ortho OUTPUT_ORTHO
                        output orthography as well as IPA
  -m DELIMIT, --delimit DELIMIT
                        produce explicitly delimited output (e.g., bi ca =
                        .b.i.33. .k.a.33.
```

The `--tokenize` flag is useful if you are processing an older source in which morphemes are separated by hyphens, and you wish to retain the hyphens in your output, or if you are processing the output of e.g. [vnTokenizer](http://mim.hus.vnu.edu.vn/phuonglh/softwares/vnTokenizer):

```
[user@terminal]$ python vPhon.py -t test/tokenized.txt 
căw24 oŋ͡m33_ta3 kuŋ͡m35g viən33 cɯə33 biət45
```

The `--delimit` flag will produce produce output where each phonetic symbol is separated by user-specified delimiter. If you use this flag, you must also specify a delimiter, e.g. 

```
[user@terminal]$ python vPhon.py -m . -8 < test/tokenized.txt
.c.ă.w.5. [ông_ta] .k.u.ŋ.3. .v.iə.n.1. .c.ɯə.1. .b.iə.t.5b.
```

The `--ortho` flag will output the orthographic input followed by a user-specified delimiter, followed by the phonetized output. If you use this flag, you must also specify a delimiter, e.g. 

```
[user@terminal]$ python vPhon.py -o , < test/wordlist-top.txt 
a dua,a33 zuə33
a ha,a33 ha33
a hoàn,a33 hwan32
a la hán,a33 la33 han24
a-lô,[a-lô]
```

```
[user@terminal]$ python vPhon.py -o $'\n' < test/wordlist-top.txt 
a dua
a33 zuə33
a ha
a33 ha33
a hoàn
a33 hwan32
a la hán
a33 la33 han24
a-lô
[a-lô]
```

## Notes

All non-alphanumeric characters in the input are stripped prior to processing (unless the `--tokenize` option is selected, in which case `-` and `_` will be retained in the output). 

Any input containing non-Vietnamese orthography, or series of characters not conforming to Vietnamese phonotactics, will be braced in the output, e.g.

```
[user@terminal] cat test/tuoi.txt | python vPhon.py 
[tt] [hacao] [linux] la32 mot21 he21g diəw32 hɛŋ32 ɲɔ312 ɣɔn21g tok͡p45 do21g dɤ̆j32 du312 tiən21g ŋi33 miən35g fi24 ma35g ŋuən32 mɤ312 dɯək21 taw21g za33 zɛŋ32 cɔ33 tɤ̆t45 ka312 mɔj21g ŋɯəj32
[hacao] [linux] kɔ24 tʰe312 căj21g cɯk21 tiəp45 cen33 [cd] ma32 xoŋ͡m33 lam32 ɛŋ312 hɯəŋ312 den24 he21g tʰoŋ͡m24 măj24 tiŋ24 hiən21g taj21g ŋwaj32 za33 [hacao] [linux] kɔn32 kɔ24 tʰe312 kaj32 dăt21 vaw32 kak45 tʰɛŋ33 ɲɤ24 [usb] hăj33 kaj32 tʰăŋ312 vaw32 o312 kɯŋ24 măj24 tiŋ24
hiən21g năj33 [hacao] [linux] kɔ24 haj33 fiən33 ban312
fiən33 ban312 ɲɔ312 ɣɔn21g tok͡p45 do21g zɛŋ32 cɔ33 ɲɯŋ35g ŋɯəj32 iəw33 tʰik45 sɯ21g ɲɔ312 ɣɔn21g [79,5mb]
```

You could also extract just these items, e.g.

```
[user@terminal] cat test/tuoi.txt | python vPhon.py | awk -F '[][]' '{for (i=2; i<=NF; i+=2) {printf "%s ", $i}; print ""}'
tt hacao linux 
hacao linux cd hacao linux usb 
hacao linux 
79,5mb 
```

Try running the examples in the `test/` directory to get a better idea of this behavior. 

## Citation

If you use vPhon for a project or paper, please cite it as:

    Kirby, James. 2008. vPhon: a Vietnamese phonetizer. Retrieved on <date> from http://github.com/kirbyj/vPhon/.
    
## Alternatives

[ADRPhone](http://www.mica.edu.vn/ADRPhone) is a lightweight, standalone phonetizer for Vietnamese written in standard C by Nguyễn Thị Minh Tuyền and Mathias Rossignol. It has many of the same functions as vPhon, but helpfully outputs XML as well. 

## Thank You

Thanks to Doug Cooper, Paul Sidwell, Marc Brunelle, Mark Alves, James Myers, and Luke Bradley for many useful comments and suggestions, and to [Branislav Gerazov](https://github.com/gerazov) and Jia-Cing Ruan (阮家慶) for their help with the Python 3 port. Any errors or inconsistencies are, of course, mine alone, but I would love to hear about them.

## References

Alves, Mark J. (2007a). "A look at North-Central Vietnamese." In *SEALS XII*, ed. R. Wayland et al., Canberra, Australia, pp. 1-7.

Alves, Mark J. and Nguyễn Duy Hương. (2007b). "Notes on Thanh-Chương Vietnamese in Nghệ-an Province". In *SEALS VIII*, ed. M. Alves et al., Canberra, Australia, pp. 1-9.

Cao Xuân Hạo. (1998). *Tiếng Việt – Mấy vấn đề ngữ âm, ngữ pháp và ngữ nghĩa*. Hà Nội: NXB Giáo dục.

Emeneau, M. B. (1951). *Studies in Vietnamese (Annamese) grammar*. University of California publications in linguistics (Vol. 8). Berkeley: University of California Press.

Hoàng Thị Châu. (1989). *Tiếng Việt trên các miền đất nước: Phương ngữ học*. Hà Nội: Khoa học Xã hội.

Michaud, Alexis. (2004). Final consonants and glottalization: New perspectives from Hanoi Vietnamese. *Phonetica* 61, 119-146.

Nguyễn Đình-Hoà. (1997). *Vietnamese: Tiếng Việt không son phấn*. Amsterdam: John Benjamins Publishing Company.

Nguyễn Văn Lợi (2012). Hệ thống thanh điệu tiếng Huế (dựa trên kết quả khảo nghiệm bằng computer). *Tạp chí Từ điển học & Bách khoa thư* 5(19), 54-65.

Nguyễn Văn Lợi & Edmondson, Jerold A. (1997). Tones and voice quality in modern northern Vietnamese: Instrumental case studies. *Mon-Khmer Studies* 28, 1-18.

Phạm, Andrea. (2001). *Vietnamese tone: a new analysis*. University of Toronto dissertation. [Reprinted by Routledge, 2003.]

Thompson, Laurence E. (1965). *A Vietnamese reference grammar*. Seattle: University of Washington Press.

Vũ, T. P. (1982). Phonetic properties of Vietnamese tones across dialects. In *Papers in Southeast Asian Linguistics Volume 8 - Tonation*, ed. D. Bradley. Sydney, Australian National University, pp. 55-75.
