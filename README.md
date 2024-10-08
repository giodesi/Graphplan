
WELCOME TO GRAPHPLAN
-----------------
Main Graphplan repository for Fundamentals of Artificial Intelligence course.


'Source' folder contains the materials taken from https://www.cs.cmu.edu/~avrim/graphplan.html
basically (for Mac [Apple Silicon] users) downloading the source code (Source.tar.gz), extracting and compiling it through the 
command 'make' from the terminal in the location in which the code is extracted (before compiling, open the file 'lex.yy.c' and add the 
following '#include <unistd.h>' just after '#include <stdio.h>' at the very top of the file).

'cart_example_gp' folder contains a basic example of graphplan application.


Here is some information on running Graphplan, and on writing your own
domains and problems for it.

RUNNING GRAPHPLAN
-----------------
Decstation object code is in "graphplan" and sparc object code
is in "graphplan.sparc".  If you are accessing this through the Web,
you should set your "load to local disk" option and copy the files you
want to your machine.  The sparc code is a newer version.


To run, just type the program name. It will ask for an "operator" file
that corresponds to a domain in Prodigy, and then it will ask for a
"facts" file that corresponds to a problem in Prodigy.  Our naming
convention is:

	operator files look like: domainname_ops
	facts files look like: domainname_facts[modifier]

Graphplan will then ask for some options.  If you just hit return for
all of them, it will run in its default mode, returning a plan when it
finds one.  Some interesting options: 

Info type: enter "1" if you want to see what the action-levels of the
	graph look like and to see it searching through goal sets.  
	Enter "2" if you want more gory detail.

X viewing: Use this to see an animation of what is going on.  Also
        allows you to save the results to a postscript file.   (For some
	reason, animation makes the DECstation code really huge, so there
	are two versions here for the DECs and the one with animation is
	gzipped.)

Other:	'I' means do a quick check to toss out irrelevant initial
	    conditions.  See, for example, "rocket_factsBIG"

	'L' is a speedup using the reasoning: if I have m goals with
	    the property that I can create at most one of them in a
	    given time step, then I won't be able to find a plan of
	    fewer than m time-steps.  For instance, in a TSP problem
	    if there are m places still to be visited, then it knows
	    it will fail if the current time is < m.
	    NOTE: this option currently interferes with graphplan's
	    completeness check.

	'S' is a speedup using the reasoning: if I have a set of goals
	    at time t, and I've already failed on a SUBSET of these
	    goals at time t, then we're going to fail.  Unfortunately,
	    it seems computationally difficult to make this check, so
	    really what Graphplan does with this option is check
	    whether it has previously failed on some subset of size
	    ONE SMALLER than the current set. 

	'E' turns off some of graphplan's smarts.

We may have some other options available that we are currently playing with.

There are also command line arguments. Type "graphplan -h" to get a list.

	
FILE FORMATS
------------

The format for the operator and fact files can be seen in the
examples. Graphplan takes operator files in three formats: (1) our own
simplified Prodigy-like syntax, (2) Actual Prodigy syntax, and (3) an
older version of (1).  [In format (3), the program assumes that any
precondition not explicitly listed in the effects list is deleted].   If
the file name ends in ".lisp" then the program assumes format (2),
otherwise it assumes format (1), unless the very first character in
the file is "#" in which case it assumes format (3).

(We may at some point do away with format 3).

A few key points:

A. There is currently no inference of "not".  In fact, graphplan just
treats "not" as a string of characters, just like "in" or "have".
If you have an operator A that requires P to be false then
you need to define a new proposition Q that just happens to be
equivalent to (not P).  For instance, if P is, say,
(on-ground <y>), then we might have Q be (not on-ground <y>), or
(not-on-ground <y>) or (up-in-the-air <y>).

B. In formats (1) and (3), the way to delete something is to put the
word "del" as the first element of the list.  E.g., (del on-ground <y>) 
deletes (on-ground <y>).

C. You can't use the underscore character "_" in any token.  I.e., write
"on-ground" instead of "on_ground".

D. It is assumed that if two parameters to a given operator
have different names, like <x>, <y>, then they MUST match to values
with different names.  This will hold until we can figure out what the
right semantics of adding and deleting the same thing should be....

E. In format (2), our parser only understands comments beginning with
a ";".  Also, no inference or control rules, no lisp functions, and no
type heirarchies.


For the format of facts (problem) files, see any of the "_facts" files.
These consist of a list of types (no inheritance) followed by the
starting facts (in a list beginning with the keyword "preconds")
followed by the desired goals (in a list beginning with the keyword
"effects"). 

-----------------------------------------------------------------------
Description of worlds in this directory:

blocks: standard blocks world. "blocks_ops" in format (3), 
	"blocks_ops.lisp" in format (2).

        the domain "blocks_facts_impossible" is a simple example of
	an interesting unsolvable problem.

fixit:  Russell's flat tire domain.  Some of the names have been changed
	for increased readability (e.g., "not unfastened" --> "fastened")

link30: Link-repeat domain from [VB94]

logistics: logistics domain with trucks and planes, from Prodigy (in
	particular, from [Veloso '92])

mblocks: Like blocks but with multiple robot arms.

monkey: monkey and bananas. From UCPOP from Prodigy.

rocket: rocket domain, allowing for multiple rockets.  See if you can
	figure out what's going on in "rocket_facts_fun".

	In "rocket_factsBIG" is a domain that requires the "I" option
	(graphplan has an internal array that is exceeded without it
	-- we will change the program to be more flexible "real soon")
	
rocket_original: original rocket domain from Prodigy

rocketf: Just like rocket, but now fuel is a cargo.

testcase: a "bicycles and cars" matching problem.  "vehicles" (bikes)
	can only go nearby, but "multiuse" (cars) can go near or far. 

BW.tar.gz: [BW] artificial domains. tar'ed and gzip'ed.

---------------------------------------------------------------------

Here is a sample run, with comments in [[BRACKETS]]. The program has
changed slightly since this trace....

% graphplan
give file name for operators: fixit_ops  [[THIS IS LIKE A PRODIGY DOMAIN FILE]]
open
close
fetch
put-away
loosen
tighten
jack-up
jack-down
undo
do-up
remove-wheel
put-on-wheel
inflate
cuss
ops loaded.
give file name for initial facts: fixit_facts1  [[ LIKE A PRODIGY PROB FILE]]
facts loaded.
number of time steps, or <CR> for automatic:    [[ I HIT <CR> ]]
Info type: (2 = max, 0 or <CR> = min):          [[ I HIT <CR> ]]
Other: 'I' = look for irrelevants,
       'L' = Lower bound time needed by counting steps
       'D' = Don't do goals checking (for testing)
       'E' = Don't do mutual exclusions (for testing)
       'S' = examine subsets:                   [[ I HIT <CR> ]]
time: 1, 13 facts and 1 exclusive pairs.
time: 2, 17 facts and 9 exclusive pairs.
time: 3, 20 facts and 13 exclusive pairs.
time: 4, 20 facts and 10 exclusive pairs.
time: 5, 22 facts and 24 exclusive pairs.
time: 6, 25 facts and 38 exclusive pairs.
time: 7, 27 facts and 41 exclusive pairs.
Goals reachable at 7 steps but mutually exclusive.
time: 8, 27 facts and 30 exclusive pairs.
Goals reachable at 8 steps but mutually exclusive.
time: 9, 27 facts and 28 exclusive pairs.
Goals first reachable in 9 steps.             [[ THIS IS THE FIRST TIME THAT 
ALL GOALS APPEAR IN THE GRAPH AND NO PAIR IS MARKED AS MUTUALLY EXCLUSIVE]]

546 nodes created.
goals at time 10:
  on_wheel2_the-hub in_wheel1_boot inflated_wheel2 in_wrench_boot in_jack_boot in_pump_boot tight_nuts_the-hub closed_boot

Can't solve in 9 steps       [[ FAILED ON 9 STEPS SO TRY 10 ]]
time: 10, 27 facts and 28 exclusive pairs.
80 new nodes added.
goals at time 11:
  on_wheel2_the-hub in_wheel1_boot inflated_wheel2 in_wrench_boot in_jack_boot in_pump_boot tight_nuts_the-hub closed_boot

Can't solve in 10 steps      [[ FAILED ON 10 STEPS SO TRY 11 ]]
time: 11, 27 facts and 28 exclusive pairs.
80 new nodes added.
goals at time 12:
  on_wheel2_the-hub in_wheel1_boot inflated_wheel2 in_wrench_boot in_jack_boot in_pump_boot tight_nuts_the-hub closed_boot

Can't solve in 11 steps      [[ FAILED ON 11 STEPS SO TRY 12 ]]
time: 12, 27 facts and 28 exclusive pairs.
80 new nodes added.
goals at time 13:     
  on_wheel2_the-hub in_wheel1_boot inflated_wheel2 in_wrench_boot in_jack_boot in_pump_boot tight_nuts_the-hub closed_boot

1 open_boot                  [[ 12 STEPS WORKED! (ALL ACTIONS HAVING THE SAME
2 fetch_wrench_boot             NUMBER REPRESENT ACTIONS THAT COULD BE DONE IN
2 fetch_pump_boot               ANY ORDER (OR IN PARALLEL) ]]   
2 fetch_jack_boot
2 fetch_wheel2_boot
3 inflate_wheel2
3 loosen_nuts_the-hub
4 put-away_pump_boot
4 jack-up_the-hub
5 undo_nuts_the-hub
6 remove-wheel_wheel1_the-hub
7 put-on-wheel_wheel2_the-hub
7 put-away_wheel1_boot
8 do-up_nuts_the-hub
9 jack-down_the-hub
10 put-away_jack_boot
10 tighten_nuts_the-hub
11 put-away_wrench_boot
12 close_boot
76 entries in hash table, 153 hash hits, avg set size 5.
240 total set-creation steps (entries + hits + plan length - 1).
320 actions tried
  2.89 secs

[[The number of "set-creation steps" and the number of "actions tried"
roughly correspond to the number of nodes in a searcher like Prodigy.
"actions tried" is the number of non-noop actions attepted.  The time 
(2.89 secs) is longer than reported in the paper because of the time
used in printing to the screen]]


=======================================================================
		DISCLAIMER
		----------
This software is made available AS IS, and neither the authors nor CMU
make any warranty about the software or its performance!!
=======================================================================
