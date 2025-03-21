#!/usr/bin/perl
use strict;
use warnings;
use Plack::Request;

my $filename = "messages.txt";

my $app = sub {
    my $env = shift;
    my $req = Plack::Request->new($env);

    if ($req->method eq 'POST') {
        my $message = $req->parameters->{'message'} // '';
        open(my $fh, '>>', $filename) or die "Could not open file.";
        print $fh "$message\n";
        close($fh);
    }

    my $messages = "";
    if (-e $filename) {
        open(my $fh, '<', $filename);
        $messages .= "<li>" . $_ . "</li>" while (<$fh>);
        close($fh);
    }

    return [
        200,
        ['Content-Type' => 'text/html'],
["<html><body>
        <h2>Message Board</h2>
        <form method='POST'>
            <input type='text' name='message' required>
            <button type='submit'>Post</button>
        </form>
        <h3>Messages:</h3>
        <ul>$messages</ul>
        </body></html>"]
    ];
};

$app;