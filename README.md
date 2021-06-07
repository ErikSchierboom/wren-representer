# Wren Representer

Part of the Exercism [Wren Stack](https://github.com/exercism/wren#the-exercism-wren-stack).

The Wren Representer takes input Wren source and generates a normalized representation of that source with most of the named symbols replaced with idenfitiers instead.  The output format format resembles Wren source except:

- Most identifiers have been replaced with placeholders
- Every parsed token has a space between it and the next token.

### Goals

- Piggy-back on the work already done with Lezer for CodeMirror
- Have a human-readable-ish representation that is short and succinct

### How it works

- The tree representation generated by parsing with the Lezer parser is used to walk over the source.
- Nodes containing identifiers are normalized  (class names, method definitions, variable names, etc)
- Method dispatch is not currently normalized
- Known system classes (`String`, `Num`, etc) are left intact
- The method `new` is always left intact as a convention
- Multiple files are separated by `---` and ordered by filename (with the main solution file always leading)

#### Why not use an AST?

- There was no readily available way to generate an AST.
- The output from Lezer could be used to create an AST, but that would require extra effort.
- Personally I think a succinct "source like" format is actually better than AST.

### What it looks like

For example if given a directory of two files:

```
// rna_lib.wren
var LIB = [1,2,3]

// rna.wren
var DNA_TO_RNA = {
  "G": "C",
  "C": "G",
  "T": "A",
  "A": "U"
}

class DNA {
  static toRNA(strand) {
    return strand.map {|x| DNA_TO_RNA[x] }.join("")
  }
}
```

The representation would be:

```
var @IDENT1@ = { " A " : " U " , " C " : " G " , " G " : " C " , " T " : " A " }
class @IDENT2@ {
static @IDENT3@ ( @IDENT4@ )
{
return
@IDENT4@ . map { | @IDENT5@ |
@IDENT1@ [ @IDENT5@ ] } . join ( " " ) } }
----
var @IDENT6@ = [ 1 , 2 , 3 ]
----
```