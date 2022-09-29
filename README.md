[![Kwalitee status](https://cpants.cpanauthors.org/dist/Types-Mojo.png)](https://cpants.cpanauthors.org/dist/Types-Mojo)
[![GitHub issues](https://img.shields.io/github/issues/reneeb/Types-Mojo.svg)](https://github.com/reneeb/Types-Mojo/issues)
[![CPAN Cover Status](https://cpancoverbadge.perl-services.de/Types-Mojo-0.06)](https://cpancoverbadge.perl-services.de/Types-Mojo-0.06)
[![Cpan license](https://img.shields.io/cpan/l/Types-Mojo.svg)](https://metacpan.org/release/Types-Mojo)

# NAME

Types::Mojo - Types related to Mojo

# VERSION

version 0.06

# SYNOPSIS

```perl
package MyClass;

use Moo;
use Types::Mojo qw(MojoFile MojoCollection);
use Types::Standard qw(Int);

has file => ( is => 'rw', isa => MojoFile, coerce => 1 );
has coll => ( is => 'rw', isa => MojoCollection, coerce => 1 );
has ints => ( is => 'rw', isa => MojoCollection[Int] );

1;
```

In the script

```perl
use MyClass;
my $object = MyClass->new( file => __FILE__ ); # will be coerced into a Mojo::File object
say $object->file->move_to( '/path/to/new/location' );

my $object2 = MyClass->new( coll => [qw/a b/] );
$object2->coll->each(sub {
    say $_;
});
```

# TYPES

## MojoCollection\[\`a\]

An object of [Mojo::Collection](https://metacpan.org/pod/Mojo%3A%3ACollection). Can be parameterized with an other [Type::Tiny](https://metacpan.org/pod/Type%3A%3ATiny) type.

```perl
has ints => ( is => 'rw', isa => MojoCollection[Int] );
```

will accept only a `Mojo::Collection` of integers.

## MojoFile

An object of [Mojo::File](https://metacpan.org/pod/Mojo%3A%3AFile)

## MojoFileList

A `MojoCollection` of `MojoFile`s.

## MojoURL\[\`a\]

An object of [Mojo::URL](https://metacpan.org/pod/Mojo%3A%3AURL). Can be parameterized with a scheme.

```perl
has http_url => ( is => 'rw', isa => MojoURL["https?"] ); # s? means plain or secure -> http or https
has ftp_url  => ( is => 'rw', isa => MojoURL["ftp"] );
```

## MojoUserAgent

An object of [Mojo::UserAgent](https://metacpan.org/pod/Mojo%3A%3AUserAgent)

# COERCIONS

These coercions are defined.

## To MojoCollection

- Array reference to MojoCollection

    In a class

    ```perl
    package Test;

    use Moo;
    use Types::Mojo qw(MojoCollection);

    has 'collection' => ( is => 'ro', isa => MojoCollection, coerce => 1 );

    1;
    ```

    In the script

    ```perl
    use Test;

    use v5.22;
    use feature 'postderef';

    my $obj = Test->new(
        collection => [ 1, 2 ],
    );

    my $sqrs = $obj->collection->map( sub { $_ ** 2 } );
    say $_ for $sqrs->to_array->@*;
    ```

## To MojoFile

- String to MojoFile

    In a class

    ```perl
    package Test;

    use Moo;
    use Types::Mojo qw(MojoFile);

    has 'file' => ( is => 'ro', isa => MojoFile, coerce => 1 );

    1;
    ```

    In the script

    ```perl
    use Test;

    use v5.22;

    my $obj = Test->new(
        file => __FILE__,
    );

    say $obj->file->slurp;
    ```

## To MojoFileList

- MojoCollection of Strings

    In a class

    ```perl
    package Test;

    use Moo;
    use Types::Mojo qw(MojoFile);

    has 'files' => ( is => 'ro', isa => MojoFileList, coerce => 1 );

    1;
    ```

    In the script

    ```perl
    use Test;

    use v5.22;

    my $obj = Test->new(
        files => Mojo::Collection->(__FILE__),
    );

    for my $file ( @{ $obj->files->to_array } ) {
        say $file->basename;
    }
    ```

- Array of Strings

    In a class

    ```perl
    package Test;

    use Moo;
    use Types::Mojo qw(MojoFile);

    has 'files' => ( is => 'ro', isa => MojoFileList, coerce => 1 );

    1;
    ```

    In the script

    ```perl
    use Test;

    use v5.22;

    my $obj = Test->new(
        files => [__FILE__],
    );

    for my $file ( @{ $obj->files->to_array } ) {
        say $file->basename;
    }
    ```

- Array of MojoFile

    In a class

    ```perl
    package Test;

    use Moo;
    use Types::Mojo qw(MojoFileList);

    has 'files' => ( is => 'ro', isa => MojoFileList, coerce => 1 );

    1;
    ```

    In the script

    ```perl
    use Test;

    use v5.22;

    my $obj = Test->new(
        files => [Mojo::File->new(__FILE__)],
    );

    for my $file ( @{ $obj->files->to_array } ) {
        say $file->basename;
    }
    ```

## To MojoURL

- String to MojoURL

    In a class

    ```perl
    package Test;

    use Moo;
    use Types::Mojo qw(MojoFile);

    has 'file' => ( is => 'ro', isa => MojoURL, coerce => 1 );

    1;
    ```

    In the script

    ```perl
    use Test;

    use v5.22;

    my $obj = Test->new(
        url => 'http://perl-services.de',
    );

    say $obj->url->host;
    ```



# Development

The distribution is contained in a Git repository, so simply clone the
repository

```
$ git clone git://github.com/reneeb/Types-Mojo.git
```

and change into the newly-created directory.

```
$ cd Types-Mojo
```

The project uses [`Dist::Zilla`](https://metacpan.org/pod/Dist::Zilla) to
build the distribution, hence this will need to be installed before
continuing:

```
$ cpanm Dist::Zilla
```

To install the required prequisite packages, run the following set of
commands:

```
$ dzil authordeps --missing | cpanm
$ dzil listdeps --author --missing | cpanm
```

The distribution can be tested like so:

```
$ dzil test
```

To run the full set of tests (including author and release-process tests),
add the `--author` and `--release` options:

```
$ dzil test --author --release
```

# AUTHOR

Renee Baecker <reneeb@cpan.org>

# COPYRIGHT AND LICENSE

This software is Copyright (c) 2019 by Renee Baecker.

This is free software, licensed under:

```
The Artistic License 2.0 (GPL Compatible)
```
