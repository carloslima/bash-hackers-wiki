# Obsolete and deprecated syntax

![](keywords>bash shell scripting obsolete deprecated outdated)

This (incomplete) page describes some syntax and commands considered
obsolete by some measure. A thorough discussion of the rationale is
beyond the scope of this page. See the [portability
page](/scripting/nonportable) for a discussion on portability issues.

This first table lists syntax that is tolerated by Bash but has few if
any legitimate uses. These features exist mostly for Bourne, csh, or
some other backward compatibility with obsolete shells, or were
Bash-specific features considered failed experiments and deprecated or
replaced with a better alternative. These should be irrelevant to most
everyone except maybe code golfers. New scripts should never use them.
None of the items on this list are specified by the most current version
of POSIX, and some may be incompatible with POSIX.

                Syntax                                      Replacement                                                                                                                                                                                                                                                                                                                                                                                           Description
  ---------------------------------- --------------------------------------------------------- --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
        `&>FILE` and `>&FILE`                              `>FILE 2>&1`                         This redirection syntax is short for `>FILE 2>&1` and originates in the C Shell. The latter form is especially uncommon and should never be used, and the explicit form using separate redirections is preferred over both. These shortcuts contribute to confusion about the copy descriptor because the syntax is unclear. They also introduce parsing ambiguity, and conflict with POSIX. Shells without this feature treat `cmd1 &>file cmd2` as: \"background `cmd1` and then execute `cmd2` with its stdout redirected to `file`\", which is the correct interpretation of this expression. See: [redirection](/syntax/redirection) \`\$ { bash; dash \</dev/fd/0; } \<\<\<\'echo foo\>/dev/null&\>/dev/fd/2 echo bar\' foo echo bar bar\`
           `$[EXPRESSION]`                               `$((EXPRESSION))`                                                                                                                                                                                                                                    This undocumented syntax is completely replaced by the POSIX-conforming arithmetic expansion `$((EXPRESSION))`. It is unimplemented almost everywhere except Bash and Zsh. See [arithmetic expansion](/syntax/expansion/arith). [Some discussion](http://lists.gnu.org/archive/html/bug-bash/2012-04/msg00034.html).
        `COMMAND\ |&\ COMMAND`                       `COMMAND 2>&1 | COMMAND`                                                                                                                             This is an alternate pipeline operator derived from Zsh. Officially, it is not considered deprecated by Bash, but I highly discourage it. It conflicts with the list operator used for [coprocess](/syntax/keywords/coproc) creation in most Korn shells. It also has confusing behavior. The stdout is redirected first like an ordinary pipe, while the stderr is actually redirected last \-- after other redirects preceding the pipe operator. Overall, it\'s pointless syntax bloat. Use an explicit redirect instead.
   `function\ NAME()\ COMPOUND-CMD`   `NAME()\ COMPOUND-CMD` or `function\ NAME\ {\ CMDS;\ }`                                                                                                This is an amalgamation between the Korn and POSIX style function definitions - using both the `function` keyword and parentheses. It has no useful purpose and no historical basis or reason to exist. It is not specified by POSIX. It is accepted by Bash, mksh, zsh, and perhaps some other Korn shells, where it is treated as identical to the POSIX-style function. It is not accepted by AT&T ksh. It should never be used. See the next table for the `function` keyword. Bash doesn\'t have this feature documented as expressly deprecated.
           `for x; { ...;}`                      `do`, `done`, `in`, `esac`, etc.                                                                                                                                                                                                        This undocumented syntax replaces the `do` and `done` reserved words with braces. Many Korn shells support various permutations on this syntax for certain compound commands like `for`, `case`, and `while`. Which ones and certain details like whether a newline or semicolon are required vary. Only `for` works in Bash. Needless to say, don\'t use it.

This table lists syntax that is specified by POSIX (unless otherwise
specified below), but has been superseded by superior alternatives
(either in POSIX, Bash, or both), or is highly discouraged for other
reasons such as encouraging bad practices or dangerous code. Those that
are specified by POSIX may be badly designed and unchangeable for
historical reasons.

  -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
                                                 Syntax                                                                                                                      Replacement                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           Description
  ----------------------------------------------------------------------------------------------------- ----------------------------------------------------------------------------------------------------------------------------------------------------- -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
   Unquoted expansions, [wordsplit](/syntax/expansion/wordsplit), and [globs](/syntax/expansion/globs)   [Proper quoting](http://mywiki.wooledge.org/Quotes), Ksh/Bash-style [arrays](/syntax/arrays), The \"\$@\" expansion, [read](/commands/builtin/read)   *Quoting errors* are a broad category of common mistakes brought about by a few unintuitive features carried over from the Bourne shell due to complaints of broken scripts and changes in previously documented behavior. Most of the important expansions are performed at the same time from left to right. However, a few expansions, most notably word-splitting and globbing, and in shells other than Bash, [brace expansion](/syntax/expansion/brace), are performed **on the results of previous expansions, by default, unless they are quoted.** This means that the act of expanding an unquoted variable in an ordinary argument context, depending on the value of the variable, can yield different results depending on possibly uncontrolled side-effects like the value of `IFS`, and the names of files in the current working directory. You can\'t get globbing without word-splitting, or vice versa (without `set -f`). [You can\'t store a command or character-delimited list in a variable and safely evaluate it with unquoted expansion](http://mywiki.wooledge.org/BashFAQ/050). If possible, always choose a shell that supports Korn shell arrays such as Bash. They are a vital but non-standard feature for writing clean, safe scripts. Well-written scripts don\'t use word-splitting. A few exceptions are listed on the [word splitting page](/syntax/expansion/wordsplit). A significant proportion of the issues on the famous [Pitfalls list](http://mywiki.wooledge.org/BashPitfalls) fall under this category. See also: *[Don\'t read lines with for!](http://mywiki.wooledge.org/DontReadLinesWithFor)*

                                            `` `COMMANDS` ``                                                                                                                `$(COMMANDS)`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                This is the older Bourne-compatible form of the [command substitution](/syntax/expansion/cmdsubst). Both the `` `COMMANDS` `` and `$(COMMANDS)` syntaxes are specified by POSIX, but the latter is [greatly]{.underline} preferred, though the former is unfortunately still very prevalent in scripts. New-style command substitutions are widely implemented by every modern shell (and then some). The only reason for using backticks is for compatibility with a real Bourne shell (like Heirloom). Backtick command substitutions require special escaping when nested, and examples found in the wild are improperly quoted more often than not. See: *[Why is \$(\...) preferred over \`\...\` (backticks)?](http://mywiki.wooledge.org/BashFAQ/082)*.

                              `[\ EXPRESSION\ ]`\\ and\\ `test\ EXPRESSION`                                                                                             `[[\ EXPRESSION\ ]]`                                                                                                                                                                                                                                                                                                                                                                                                      `test` and `[` are the Bourne/POSIX commands for evaluating test expressions (they are almost identical, and `[` is somewhat more common). The expressions consist of regular arguments, unlike the Ksh/Bash `[[` command. While the issue is analogous to `let` vs `((`, the advantages of `[[` vs `[` are even more important because the arguments/expansions aren\'t just concatenated into one expression. With the classic `[` command, the number of arguments is significant. If at all possible, use the [conditional expression](/syntax/ccmd/conditional_expression) (\"new test command\") `[[ EXPRESSION ]]`. Unless there is a need for POSIX compatibility, there are only a few reasons to use `[`. `[[` is one of the most portable and consistent non-POSIX ksh extensions available. See: [conditional_expression](/syntax/ccmd/conditional_expression) and *[What is the difference between test, \[ and \[\[ ?](http://mywiki.wooledge.org/BashFAQ/031)*

                                       `set -e`, `set -o errexit`\                                                                                             proper control flow and error handling                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      `set -e` causes untested non-zero exit statuses to be fatal. It is a debugging feature intended for use only during development and should not be used in production code, especially init scripts and other high-availability scripts. Do not be tempted to think of this as \"error handling\"; it\'s not, it\'s just a way to find the place you\'ve *forgotten* to put error handling.\
                                           and the `ERR` trap                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               Think of it as akin to `use strict` in Perl or `throws` in C++: tough love that makes you write better code. Many guides recommend avoiding it entirely because of the apparently-complex rules for when non-zero statuses cause the script to abort. Conversely, large software projects with experienced coders may recommend or even mandate its use.\
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         Because it provides no notification of the location of the error, it\'s more useful combined with `set -x` or the `DEBUG` trap and other Bash debug features, and both flags are normally better set on the command line rather than within the script itself.\
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              Most of this also applies to the `ERR` trap, though I\'ve seen it used in a few places in shells that lack `pipefail` or `PIPESTATUS`. The `ERR` trap is not POSIX, but `set -e` is. `failglob` is another Bash feature that falls into this category (mainly useful for debugging).\
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        **The `set -e` feature generates more questions and false bug reports on the Bash mailing list than all other features combined!** Please do not rely on `set -e` for logic in scripts. If you still refuse to take this advice, make sure you understand **exactly** how it works. See: *[Why doesn\'t set -e (or set -o errexit, or trap ERR) do what I expected?](http://mywiki.wooledge.org/BashFAQ/105)* and <http://www.fvue.nl/wiki/Bash:_Error_handling>

                                      `set -u` or `set -o nounset`                                                                                             Proper control flow and error handling                                                                                                                                                                                                                                                                                                                                                                                                                                                       `set -u` causes attempts to expand unset variables or parameters as fatal errors. Like `set -e`, it bypasses control flow and exits immediately from the current shell environment. Like non-zero statuses, unset variables are a normal part of most non-trivial shell scripts. Living with `set -u` requires hacks like `${1+"$1"}` for each expansion that might possibly be unset. Only very current shells guarantee that expanding `@` or `*` won\'t trigger an error when no parameters are set (<http://austingroupbugs.net/view.php?id=155>, <http://www.in-ulm.de/~mascheck/various/bourne_args/>). Apparently some find it useful for debugging. See *[How do I determine whether a variable is already defined? Or a function?](http://mywiki.wooledge.org/BashFAQ/083)* for how to properly test for defined variables. Don\'t use `set -u`.

                                      `${var?msg}` or `${var:?msg}`                                                                                            Proper control flow and error handling                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   Like `set -u`, this expansion causes a fatal error which immediately exits the current shell environment if the given parameter is unset or is null. It prints the error message given, to the right of the operator. If a value is expected and you\'d like to create an assertion or cause errors, it\'s better to test for undefined variables using one of [these techniques](http://mywiki.wooledge.org/BashFAQ/083) and handle the error manually, or call a `die` function. This expansion is defined by POSIX. It\'s better than `set -u`, because it\'s explicit, but not by much. It also allows you to accidentally construct hilariously deceptive error messages: `bash -c 'f() { definitely_not_printf "${printf:?"$1" - No such option}"; }; f -v'
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       bash: printf: -v - No such option`
  -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

This table lists features that are used only if you have a specific
reason to prefer it over another alternative. These have some legitimate
uses if you know what you\'re doing, such as for those with specific
portability requirements, or in order to make use of some subtle
behavioral differences. These are frequently (mis)used for no reason.
Writing portable scripts that go outside of POSIX features requires
knowing how to account for many (often undocumented) differences across
many shells. If you do happen to know what you\'re doing, don\'t be too
surprised if you run across someone telling you not to use these.

              Syntax                                           Replacement                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 Description
  ------------------------------- --------------------------------------------------------------------- ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
   `function\ NAME\ {\ CMDS;\ }`                         `NAME()\ COMPOUND-CMD`                                                        This is the ksh form of function definition created to extend the Bourne and POSIX form with modified behaviors and additional features like local variables. The idea was for new-style functions to be analogous to regular builtins with their own environment and scope, while POSIX-style functions are more like special builtins. `function` is supported by almost every ksh-derived shell including Bash and Zsh, but isn\'t specified by POSIX. Bash treats all function styles the same, but this is unusual. `function` has some preferable characteristics in many ksh variants, making it more portable for scripts that use non-POSIX extensions by some measures. If you\'re going to use the `function` keyword, it implies that you\'re either targeting Ksh specifically, or that you have detailed knowledge of how to compensate for differences across shells. It should always be used consistently with `typeset`, but never used with `declare` or `local`. Also in ksh93, the braces are not a [command group](/syntax/ccmd/grouping_plain), but a required part of the syntax (unlike Bash and others). See [shell function definitions](/syntax/basicgrammar#shell_function_definitions)
             `typeset`                          `declare`, `local`, `export`, `readonly`                                                                                                                                                                                                                                             This is closely related to the above, and should often be used together. `typeset` exists primarily for `ksh` compatibility, but is marked as \"deprecated\" in Bash (though I don\'t entirely agree with this). This makes some sense, because future compatibility can\'t be guaranteed, and any compatibility at all, requires understanding the non-POSIX features of other shells and their differences. Using `declare` instead of `typeset` emphasizes your intention to be \"Bash-only\", and definitely breaks everywhere else (except possibly zsh if you\'re lucky). The issue is further complicated by Dash and the [Debian policy](http://www.debian.org/doc/debian-policy/ch-files.html#s-scripts) requirement for a `local` builtin, which is itself not entirely compatible with Bash and other shells.
       \'\'let \'EXPR\' \'\'                     `((EXPR))` or `[\ $((EXPR))\ -ne\ 0 ]`                                                                                                                                                                                                                                                                                      `let` is the \"simple command\" variant of arithmetic evaluation command, which takes regular arguments. Both `let` and `((expr))` were present in ksh88, and everything that supports one should support the other. Neither are POSIX. The compound variant is preferable because it doesn\'t take regular arguments for [wordsplitting](/syntax/expansion/wordsplit) and [globbing](/syntax/expansion/globs), which makes it safer and clearer. It is also usually faster, especially in Bash, where compound commands are typically significantly faster. Some of the (few) reasons for using `let` are detailed on the [let](/commands/builtin/let) page. See [arithmetic evaluation compound command](/syntax/ccmd/arithmetic_eval)
              `eval`               Depends. Often code can be restructured to use better alternatives.   `eval` is thrown in here for good measure, as sadly it is so often misused that any use of `eval` (even the rare clever one) is immediately dismissed as wrong by experts, and among the most immediate solutions abused by beginners. In reality, there are correct ways to use `eval`, and even cases in which it\'s necessary, even in sophisticated shells like Bash and Ksh. `eval` is unusual in that it is less frequently appropriate in more feature-rich shells than in more minimal shells like Dash, where it is used to compensate for more limitations. If you find yourself needing `eval` too frequently, it might be a sign that you\'re either better off using a different language entirely, or trying to borrow an idiom from some other paradigm that isn\'t well suited to the shell language. By the same token, there are some cases in which working too hard to avoid `eval` ends up adding a lot of complexity and sacrificing all portability. Don\'t substitute a clever `eval` for something that\'s a bit \"too clever\", just to avoid the `eval`, yet, take reasonable measures to avoid it where it is sensible to do so. See: [eval](/commands/builtin/eval) and [Eval command and security issues](http://mywiki.wooledge.org/BashFAQ/048).

## See also

-   [Non-portable syntax and command uses](/scripting/nonportable)
-   [bashchanges](/scripting/bashchanges)
-   [Greg\'s BashFAQ 061: List of essential features added (with the
    Bash version tag)](BashFAQ>061)
-   [Bash \<-\> POSIX Portability guide with a focus on
    Dash](http://mywiki.wooledge.org/Bashism)
-   <http://mywiki.wooledge.org/BashPitfalls>