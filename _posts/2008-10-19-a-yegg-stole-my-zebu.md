---
layout: post
title: A yegg stole my zebu
date: 2008-10-19
---

A while ago I received one of those annoying chain emails which ask you to take the last word in a list, change one letter to find a valid word not already in the list, add it to the end and forward the whole thing to ten friends or you'll die in a horrible car crash.

It got me wondering about words in English. Are most words connected to each other in this way? If not, what do the distinct groups (or <a href="http://en.wikipedia.org/wiki/Clique_%28graph_theory%29">cliques</a> in graph parlance) look like? Do two-letter words, three-letter words or four-letter words have more cliques? I decided I'd find out.

## Preliminaries

For simplicity, I limited the edit operation to only allow one letter in the word to change (ie. no deletions or insertions) at a time. You can think of the dictionary as an undirected graph, with each node being a word and each edge an edit operation permitting you to travel between adjacent nodes.

My data source is the <a href="http://wordlist.sourceforge.net/12dicts-readme.html#2of12inf">2of12inf.txt</a> file from the <a href="http://wordlist.sourceforge.net/">12 Dicts package from wordlists.sourceforge.net</a>. It uses American spellings and seems to be a fairly decent list of words (which is to say my 10 minutes of hunting didn't provide me with anything better). It contains:

<ul>
<li>2-letter words: 62</li>
<li>3-letter words: 642</li>
<li>4-letter words: 2546</li>
<li>5-letter words: 5122</li>
</ul>

## Results

A little ruby script yielded the following results.

All **2-letter words** belong to the same clique.

Of the **3-letter words**, 631 (of 642) belong to the same clique, the remaining 11 are each entirely disconnected from each other. They are,

<ul>
<li>nth, ism, urn, ebb, obi, qua, ova, use, ugh, gnu, aha</li>
</ul>

The **4-letter words** are more interesting. 2415 of the 2546 belong to the same clique and the other 131 are divided up into 97 other cliques. Many of the 131 are common words. The 18 cliques with more than one word:

<ul>
<li>ache, achy, acme, acne, acre, ashy
</li><li>info, into, onto, undo, unto
</li><li>high, nigh, sigh, sign
</li><li>afar, agar, ajar
</li><li>also, alto, auto
</li><li>eddy, edge, edgy
</li><li>icon, ikon, iron
</li><li>idle, idly, isle
</li><li>opal, oral, oval
</li><li>used, user, uses
</li><li>bevy, levy
</li><li>crud, crux
</li><li>demo, memo
</li><li>hadj, hajj
</li><li>idol, idyl
</li><li>ogle, ogre
</li><li>orzo, ouzo
</li><li>thou, thru</li></ul>

The remaining 79 that are stranded on their own:

<ul><li>adze, agog, ague, ahoy, alga, ammo, amok, anal, ankh, apse, aqua, aura, avow, awol, ayah, bozo, ciao, ditz, ebbs, echo, ecru, egad, emus, ends, envy, epee, epic, espy, euro, evil, exam, expo, guru, hymn, ibex, iffy, imam, iota, isms, jato, judo, kiwi, liar, luau, lynx, meow, myna, nevi, nova, obey, oboe, odor, ohms, okay, okra, once, onyx, orgy, ovum, rely, rhea, rhos, semi, sexy, stye, sync, tofu, tuft, ugly, ulna, upon, urge, uric, urns, void, yegg, yeti, yuan, zebu</li></ul>

The **5-letter words** have the most room for sizeable subsets. Of the 5122 words, 3935 fall into a common clique. The next 5 cliques with at least 10 members:

<ul><li>reset, resew, resow, renew, beset, besot, besom, bosom, begot, begat, began, begun, begum, begin, vegan, bigot, bight, wight, tight, sight, sighs, signs, highs, right, night, might, light, fight, eight, beret, beget (31)
</li><li>round, wound, would, world, mould, moult, mount, fount, count, court, could, sound, pound, mound, hound, found, bound (17)
</li><li>acnes, acres, acmes, aches, ached, acted, anted, antes, antis, antic, attic, ashes, ashen, aspen, asses, asset, apses (17)
</li><li>comic, conic, cynic, tonic, toxic, toxin, topic, tunic, runic, sonic, ionic, colic (12)
</li><li>overs, overt, avert, alert, ovens, opens, omens, evens, event, avers (10)</li></ul>The next 31 cliques have between 4 and 9 members, 34 have 3 members each, 108 have 2 members each, and 613 words are stranded on their own.

The joys of a Sunday evening well-spent. I also learnt that <i>nth</i> is a valid word without vowels, a <a href="http://en.wikipedia.org/wiki/Zebu">zebu</a> is a breed of cattle, and a <a href="http://www.answers.com/yegg">yegg</a> is a burglar or safecracker.
