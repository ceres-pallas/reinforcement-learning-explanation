<style type="text/css">
td {
	text-align: center;
	width: 40px;
}
tr {
	height: 40px;
}

</style>

>> Imagine playing a new game whose rules you don't know; after a hundred or so moves, your opponent announces, “You lose”.
‐
Russell and Norvig: Artificial Intelligence: A Modern Approach



## Reinforcement ##
In behavioral psychology, reinforcement is a consequence that will strengthen an organism's future behavior whenever that behavior is preceded by a specific antecedent stimulus.
## Learning ##
Learning is the act of acquiring new, or modifying and reinforcing, existing knowledge, behaviors, skills, values, or preferences and may involve synthesizing different types of information.

# Reinforcement Learning #

When I want someone to learn something, I give them something to read or I tell them how to perform some list of actions to reach a goal.

In reinforcement learning (RL), we try to adjust your behaviour by either rewarding or punishing you after you’ve performed some list of actions and leave it up to you to reevaluate what to change.
 
## Examples, examples, examples ##

If we consider some random puzzle X, which is in a state Si. What we want to do, is manipulate the problem until we reach a state Sn that ends that puzzle.  

Let's take an often used example from Russel & Norvig's "Artificial Intelligence: A Modern Approach", a book that is worth having. 

The following 'maze' has a width of 4 squares and a height of 3. (0,0) is S<sub>0</sub>. Our agent always starts in that position. (1,1) cannot be passed through. (3,2) is our "positive" endstate and (3,1) our "negative" endstate.

Reaching an endstate, ends our problem and gives the agent either a positive reinforcement of 1 or a negative reinforcement that equals -1.

<table>
<tr>
<td></td><td></td><td></td><td>+1</td>
</tr>
<tr>
<td></td><td style="background-color:black;"></td><td></td><td>-1</td>
</tr>
<tr>
<td>&#9786;</td><td></td><td></td><td></td>
</tr>
</table>

Our valiant agent does not see what we see. It does not know this world, it does not have to. It only knows where it is and it knows what actions it can perform to transition out of this state. 

So from S<sub>0</sub> or S(0,0) it can either move up, or down. In our example we want to encourage our agent to learn that a shorter path is better than the long way around. So let's make the cost of an action 0.04.

The idea is that our agent evaluates a state according to it's Reward-to-go; Reward from that state onwards. So *&mu;(3,2) = 1* and *&mu;(3,1) = -1*. If we consider S(2,2) and move right, the transition cost with the positive reinforcement will result in *&mu;(2,2) = 0.96*. So there is an expected reward  when we for right from S(2,2).

If there is nothing we know, all we can do is move around.
From S<sub>0</sub> we perform the following 'random' set of actions: &uArr; &uArr; &rArr; &rArr; &rArr;

Our policy (which is conincidentally our optimal policy) will be rewarded with a utility of *1-5\*0.04*. Now, we want to propagate back over our actions where we observe the following *actual* utilities which directly correspond to the Reward-to-go of that state in this trial:

*&mu;(3,2) = 1* <br>
*&mu;(2,2) = 0.96* <br> 
*&mu;(2,2) = 0.92* <br>
*&mu;(2,2) = 0.88* <br>
*&mu;(2,2) = 0.84* <br>
*&mu;(0,0) = 0.80* <br>

Because we do not want to overfit to the data, we introduce a learning parameter. &sigma; = 0.1 and use that to adjust the utilities of the states.

<table>
<tr>
<td>0.088</td><td>0.092</td><td>0.096</td><td>1</td>
</tr>
<tr>
<td>0.084</td><td style="background-color:black;"></td><td>0</td><td>-1</td>
</tr>
<tr>
<td>0.080</td><td>0</td><td>0</td><td>0</td>
</tr>
</table>

So now we have evaluated our actions. And if we would be greedy and restart from S(0,0), we would see *&mu;(S(1,0)) > &mu;(S(0,1))* and choose to go &uArr;. We call this *exploitation*. Exploit what we know.

But we don't want our agent to be satisfied too soon. There might be more in the world, heck, we don't know anything about the world! We introduce an exploration rate *&gamma; = 0.9*. Which means that in 90% of the cases, we do not exploit, but we explore and randomly try one of the possible actions. It is advisable to start with a very exploritory agent, to just try, fail and learn. Our exploration rate can be brought down to near zero when we feel that our estimated utilities have converged.  

In the nextr trial from S<sub>0</sub> we perform the following 'random' set of actions: &uArr; &uArr; &rArr; &rArr; &dArr; &dArr; &rArr; &uArr;

*&pi;(3,1) = -1* <br>
*&pi;(3,0) = -1.04* <br>
*&pi;(2,0) = -1.08* <br>
*&pi;(2,1) = -1.12* <br>
*&pi;(2,2) = -1.16* <br>
*&pi;(2,1) = -1.20* <br>
*&pi;(2,0) = -1.24* <br>
*&pi;(1,0) = -1.28* <br>
*&pi;(0,0) = -1.32* <br>

This is a lot different from our first trial. This set would bring our previously estimated back down to zero or even below zero. Anyway, after a few more trials we might end up with something like this:

<table>
<tr>
<td>0.01</td><td>0.07</td><td>0.26</td><td>1</td>
</tr>
<tr>
<td>-0.16</td><td style="background-color:black;"></td><td>-0.56</td><td>-1</td>
</tr>
<tr>
<td>-0.28</td><td>-0.36</td><td>-0.61</td><td>-0.81</td>
</tr>
</table>

Thus the agent has found a policy &pi; for X:

<table>
<tr>
<td>&rArr;</td><td>&rArr;</td><td>&rArr;</td><td>1</td>
</tr>
<tr>
<td>&uArr;</td><td style="background-color:black;"></td><td>&uArr;</td><td>-1</td>
</tr>
<tr>
<td>&uArr;</td><td>&lArr;</td><td>&lArr;</td><td>&lArr;</td>
</tr>
</table>

# Function approximation #
There is a different way to estimate the utility of a state. We use a function approximator (FA) to estimate utilities. The FA has a set of value functions that, combined, deliver an estimation of value of a certain state. 

This is particularly useful when the state-space becomes too large. The 'maze' problem converges only when all states have been visited, preferably a lot of times, the amount of trials that need to be run increases as the state-space grows.

Let's look at X again. If we define 3 value functions with randomly initiated weights; <br>
&fnof;<sub>0</sub>(S) = &phi; = 1, &theta;<sub>0</sub> = 0.1 <br>
&fnof;<sub>1</sub>(S) = x, &theta;<sub>1</sub> = 0.5 <br>
&fnof;<sub>2</sub>(S) = y, &theta;<sub>2</sub> = 0.2 <br>
<br>
&mu;(S) = sum of all f<sub>i</sub>(S)
<br>

These functions give the following utilities if we multiply the outcome of the function in the corresponding state with it's weight and take the sum of the outcomes:

<table>
<tr>
<td>0.5</td><td>1.0</td><td>1.5</td><td>2</td>
</tr>
<tr>
<td>0.3</td><td style="background-color:black;"></td><td>1.3</td><td>1.8</td>
</tr>
<tr>
<td>0.1</td><td>0.6</td><td>1.1</td><td>1.6</td>
</tr>
</table>

Consider the following path:
*&pi;(3,1) = -1* <br>
*&pi;(3,0) = -1.04* <br> 
*&pi;(2,0) = -1.08* <br>
*&pi;(1,0) = -1.12* <br>
*&pi;(0,0) = -1.16* <br>

We want to correct the weights of the various functions in the direction of observed utilities. 

We once again propagate back over the actions and use a learning parameter *&sigma; = 0.1*.

&theta;<sub>i</sub> = &theta;<sub>i</sub> + &sigma; * ||&pi;(S)-&mu;(S)|| * &fnof;<sub>i</sub>

So we adjust all weights in the same direction, because whatever happened, the collective result of the valuefunctions *&mu;(S)* was off by *||&pi;(S)-&mu;(S)||*. *&pi;(S)* is the actual observed result. We multiply by *&fnof;<sub>i</sub>*, so that we adjust weights according to the utility they contributed to the total estimate of the state. 

Simply put, if we look at S(1,3), the weight of the valuefunction that looks at the '3' (*&fnof;<sub>1</sub>*) is adjusted more because it is multiplied by that 3. We assume they contributed harder to the fact that our estimate is off.

Now, you need to remember that a valuefunction can be any function. If we believe that the difference between the X and the Y or the amount of actions that can be taken in a certain state is of importance, we add such a function, ie. *&fnof;<sub>3</sub>= ||x-y||*.

But we won't.

*&fnof;<sub>0</sub>* is a special case. As you have undoubtedly seen, it's value is always equal to 1, in whatever state. This is so, if there is one, a baseline to the utilities in all states can be determined. 

So, back to our path; <br> 
*&pi;(3,1) = -1* <br>
*&pi;(3,0) = -1.04* <br> 
*&pi;(2,0) = -1.08* <br>
*&pi;(1,0) = -1.12* <br>
*&pi;(0,0) = -1.16* <br>

Given the current weights, we will start our corrections from the last state. <br>
For &theta;<sub>0</sub> = 0.1 + 0.1 * ((-1) - 1.8) * 1 = 0.1 - 0.28 * 1 = -0.18<br>
For &theta;<sub>1</sub> = 0.5 + 0.1 * ((-1) - 1.8) * 3 = 0.5 - 0.28 * 3 = -0.34<br>
For &theta;<sub>2</sub> = 0.2 + 0.1 * ((-1) - 1.8) * 1 = 0.2 - 0.28 * 1 = -0.08<br>

The new weights immediately result in the following scenario:

<table>
<tr>
<td>-0.34</td><td>-0.68</td><td>-1.02</td><td>-1.36</td>
</tr>
<tr>
<td>-0.26</td><td style="background-color:black;"></td><td>-0.94</td><td>-1.28</td>
</tr>
<tr>
<td>-0.18</td><td>-0.52</td><td>-0.86</td><td>-1.20</td>
</tr>
</table>

So now we can evaluate *&pi;(3,0) = -1.04* against the NEW weights! Which means the difference should be smaller. In this case, after every trial, the first correction will be largest, afterwards there won't be much we want to do. That is because our learning rate is relatively high. 

This example is nice because it shows how we reached a tedious situation. If you follow the exploitation path, you'll be running around S<sub>0</sub> for a long time. It will take some time for the agent to get out of this situation.







