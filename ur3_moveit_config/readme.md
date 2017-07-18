Notes from tuning (Zack):

So, there's two IK plugins we can use. trac-ik and RRT. I haven't seen a huge difference between them, but haven't compared too carefully.

The bigger things that affect quality are:

- The size of the joint space. Using -pi, pi limits on all joints results in reliably logical trajectories found quickly, but often times results in weird flippings of the arm when joint limits are hit. The elbow joint must be limited to -pi, pi or less or it breaks MoveIt - since it can't actually move beyond this in collision space, it makes disjoint joint spaces or something. Using -2pi, 2pi on the other joints works, but results in really slow, and sometimes wonky trajectories (I assume a more powerful computer and more time would result in good trajectories). The sweet spot seems to be in carefully setting the joint limits as small as is reasonable for each joint. 
- The value of longest_valid_segment_fraction in ompl_planning.yaml has a huge impact. Set too high, and most trajectories will be found invalid in postprocessing. Set too low, and the computation time explodes. Tune it based on the joint limits. Also, setting it too high appears to encourage overly complex paths - a smaller value generates much more logical paths. 
- Complexity of the collision space. Haven't investigated much, but seems like # polygons not the problem, but # obstacles in path increases difficulty greatly.
- Setting of planning time, planning attempts has great impact. Planning time is a cap on total computation time. Planning attempts appears to be a cap on how many plans will be generated if there's still computation time left over. More than 1 plan is definitely better - not sure how it scales.
