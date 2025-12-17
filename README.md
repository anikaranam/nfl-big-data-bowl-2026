# Introduction

What constitutes a successful defensive play in pass coverage? At the simplest level, success can be classified as a binary outcome: a completed pass that results in a first down is a failure for the defense, while an incompletion or short gain is a success. However, this binary framing fails to capture the nuanced, continuous contributions defenders make on pass plays, particularly those that do not end in a target, a pass breakup, or a tackle at the sticks.

From a tracking data perspective, one of the most critical quantities governing pass outcomes is separation between a receiver and the nearest defender. Smaller separation naturally implies tighter coverage and a lower likelihood of conversion. A natural starting point for evaluating defensive impact, then, is to measure how close a defender is to the receiver at key moments of the play. Yet, separation alone is an incomplete measure. A defender who begins a play close to a receiver but fails to close further should be evaluated differently from a defender who starts off at a larger distance from the receiver but effectively closes down the separation gap as the play develops.

An alternative approach is to evaluate defenders based on their closing ability i.e the rate at which they reduce separation. However, closing speed in isolation is also insufficient. A defender closing rapidly from a very large initial distance may still be functionally irrelevant to the play outcome, while small changes in separation near the line-to-gain can have an outsized impact on whether a first down is converted.

Therefore, to meaningfully evaluate defensive coverage performance, both the level of separation and the dynamics of how it is reduced must be considered, along with their downstream effect on play outcomes. In this work, we introduce SEAL (Separation Elimination Against the Line-to-gain), a framework that links defender separation-closing behavior directly to first-down conversion probability.

Using a predictive model of first-down conversion, we estimate the counterfactual impact of each defender’s closing ability by asking: How would conversion probability change if this defender closed separation at an average rate for their position? By comparing predicted outcomes under observed and position-average closing behavior, SEAL produces a continuous, interpretable measure of defensive value expressed in terms of overall conversion probability reduced across all plays. This approach allows us to quantify how defenders “seal” first downs not just through coverage outcomes, but through the process by which they eliminate separation as the play unfolds.

# Analysis
We focused on a specific but common scenario: passes completed short of the line to gain. These plays create a sort of race between the receiver trying to gain extra yards and the defender trying to close the gap and make a tackle. For each play, we tracked two key moments:

Peak separation - When the receiver was most open during the play
Catch moment - How much separation remained when the ball was caught

The difference between these two moments tells us how much ground the defender was able to close while the ball was in the air. We call this the "separation closed" or "openness delta." Rather than just looking at raw numbers, we wanted to understand each defender's impact relative to what is typical for their position. This is important because there are fairly large positional and situational differences in how defenders play coverage. For example, certain players may be more "aggressive", with their main intention being to close down the receiver at max speed and make a tackle while running the risk that a missed tackle could lead to a huge gain in YAC (Yards after catch). Other players could opt for a more 'conservative' style, choosing to instead give up a few extra YAC while reducing the chances of a chunk play.

Therefore, we compared each defender's average separation closed vs. the average for all defenders at their position. Then we used a predictive model trained on thousands of plays to answer the question: "If this defender had just average closing ability for their position instead of their actual ability, how would that change the probability of the offense converting a first down?"

To estimate conversion probabilities, we trained a logistic regression model on 12 key features including yards needed after catch for a first down, nearest defender distance, receiver speed at catch, defenders within 3 and 5 yards, and several other situational factors. Logistic regression is well-suited for this task because it produces interpretable probability estimates and allows us to understand how each factor contributes to the likelihood of conversion.

The model achieved a ROC AUC score of 0.822, which indicates strong predictive performance. The ROC AUC (Receiver Operating Characteristic - Area Under Curve) measures how well the model distinguishes between plays that convert and plays that don't, with 1.0 being perfect prediction and 0.5 being no better than random guessing. A score of 0.822 means our model correctly ranks the conversion probability of two randomly selected plays (one that converted, one that didn't) about 82% of the time. This level of accuracy gives us confidence that the conversion probability changes we attribute to closing ability reflect real, measurable defensive impact rather than statistical noise.

# Discussion

<img width="696" height="490" alt="top10closers" src="https://github.com/user-attachments/assets/0aa53d3d-a82b-4d11-ac74-3487b2ea72c5" />

<img width="698" height="490" alt="bottom10closers" src="https://github.com/user-attachments/assets/453e149e-8891-472d-9aa9-219e6758724e" />

## Substantial Individual Variation in Closing Ability
The analysis revealed meaningful differences in defenders' ability to close separation gaps relative to their positional peers. Adoree' Jackson led all defenders by closing an additional 0.99 yards compared to the average cornerback, translating to a 2.63 percentage point reduction in conversion probability. Conversely, Xavien Howard, despite being widely regarded as an elite cornerback, closed 0.72 fewer yards than the position average, resulting in a 2.08 percentage point increase in conversion probability.
This 4.71 percentage point swing between the best and worst performers demonstrates that separation closing is a measurable, impactful skill that varies considerably even among starting NFL defenders. The fact that this variation exists within the same position (both Jackson and Howard are cornerbacks) suggests that closing speed is a distinct skill set independent of other coverage abilities.

## Cornerbacks Dominate the Extremes
Notably, 7 of the top 10 closers were cornerbacks, while 6 of the bottom 10 were also cornerbacks. This suggests that cornerback play exhibits the highest variance in separation closing ability. The demands of man coverage and playing in space may amplify the importance of closing speed at this position, making it a critical differentiator between elite and average cornerback play.

## Linebackers Show Consistent Mid-Range Performance
Linebackers (T.J. Edwards, Logan Wilson, Alex Anzalone, Tremaine Edmunds, Quincy Williams, Zaire Franklin) populated the middle of the top 10, showing positive but more modest impacts (+0.30 to +0.44 yards vs. position average). This consistency may reflect:

Different coverage responsibilities that limit opportunities for dramatic closing
Playing in traffic where angles and congestion matter more than pure closing speed
Zone coverage schemes that prioritize positioning over pursuit
The exception was Ernest Jones (-0.61 yards vs. average), who appeared among the worst closers, suggesting that even within linebacker subgroups, closing ability varies meaningfully.

<img width="690" height="490" alt="distancetop10" src="https://github.com/user-attachments/assets/0e706ab7-2039-46e6-a490-ba0d3ac01424" />

<img width="690" height="490" alt="distancebottom10" src="https://github.com/user-attachments/assets/f206a0a9-0eb4-455b-a896-8f0cef97dc52" />

## Magnitude of Individual Impact
The largest impacts ranged from -2.08% to +2.63%. While this may seem small, it is important to consider that over a 17-game season, a defense faces approximately 150-200 third-down attempts. A 2.5 percentage point difference translates to 3.75-5 additional/prevented first downs per season. Each additional first down allowed extends drives, increasing scoring probability. Furthermore, these impacts compound when multiple defenders exhibit closing deficiencies or excellence.

# Appendix
All code for this project can be found in the Github repository here - https://github.com/anikaranam/nfl-big-data-bowl-2026. Special thanks to Ron Yurko and the members of the Carnegie Mellon Sports Analytics Club for all the mentorship and support throughout this project.

If you would like to connect with me, you can reach me on Linkedin or via email: akarana2@andrew.cmu.edu
