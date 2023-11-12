# Emprical Software Engineering

## Steps to comduct Bayesian data analysis
-  pick a initial model (null Model)
    - Reflect the basic assumption
    - Serves as a starting point to assess the impact of additional vars 
    - Helps establish a reference for model comparison
- Incorporate prior knowledge
    - Prior provide the context and constrains to your analysis
    - It can be done by specifying prior distribution for model params based on existed info and expert opinions
- Prior check
	- is very useful for assigning sensible priors, because it can be quite hard to anticipate how priors influence the observable variables
	- whether it makes sense at all ranges of the predictor variables.
- Check diagnostics
	- Asses the model's fit and performance by conducting diagnostics
	- Includes examining residual plots, assessing convergence of Markov chains
	- Diagnostics help identidy problems with the model
- Posterior checks 
	- check the model’s implied predictions against the observed data
	    - Did the model correctly approximate the posterior distribution
			- verifying that your software worked correctly
		- How does the model fail
			- inspecting the individual cases where the model makes poor predictions, you might get an idea of how to improve the model
	- combine uncertainty about parameters(posterior distribution), with uncertainty about outcomes (likelihood function).
- Inferential statistics (if needed)
	- Done to make inferences about the model params 
	- Includes means, variances, credible intervals..
- Comparisons of models 
	- Compare models to determine which one provides the best fit to the data 
	- Techniques like WAIC
- Iterative approach

## Causal Effect
- DAG
    - introduce graphical causal models as a way to design and interpret regression models
- three different hazards
    - Multicollinearity
        - strong correlation between two or more predictor variables.
        - Example : two legs and height
    - Post-treatment bias
        - mistaken inferences arising from including variables that are consequences of other variables.
        - Example: the growth of plant under different anti-fungal soil treatments
            - treatment T influences the presence of fungus F which influences plant height at time
            - F is a post-treatment effect which means that soil treatment tell nothing about the height once we already know whether or not a plant developed fungus
    - Collider bias
        - When you condition on a collider, it creates statistical—but not necessarily causal associations among its causes.
        - Example: Happiness (H) and age (A) both cause marriage (M). 
            - Marriage is therefore a collider.
            - if we condition on marriage, then it will induce a statistical association between age and happiness.
- Confounder
    - the association between an outcome Y and a predictor of interest X is not the same as it would be, if we had experimentally determined the values of X.
    - Shut the backdoor:
        - We don’t want any spurious correlation sneaking in through a non-causal path
        - Steps (control the variables)
            - List the path connecting the X and Y
            - Classify if the path is open or close
            - Classify if the path is a backdoor path
            - Condition on the variables to close the path if it is open and a backdoor path.
- four types of variable relations
    - Fork:
		- If we condition on Z, then learning X tells us nothing about Y
		- X and Y are independent, conditional on Z.
	- Pipe
		- If we condition on Z now, we also block the path from X to Y.
    - Collider:
        - Conditioning on Z, it opens the path
        - Once the path is open, information flows between X and Y
    - Descendant
        - Control for D will also control for Z in a less extend.
        - This will (partially) open the path from X to Y, because Z is a collider.

## Likelihood: 
- Principles to choose likelihood
    - Epistemological
        - rooted in information theory and the concept of maximum entropy distributions 
        - the way nature works, that is a physical assumption about the world
    - Ontological:  
        - repeatedly adding finite fluctuations results in a distribution of sums that have shed all information about the underlying process, aside from mean and spread.
    - Example: 
        - Scenario where we study the heights of a population of adult humans
            - epistemological justification:
                - Data-driven
                -  based on empirical evidence from our collected data
                - We collect a large dataset of heights measurements from a representative sample 
                - We analyze the data and find that the distribution appears to follow a bell-shaped curve when visualized as a histogram 
                - This suggests that a normal distribution might be a good fit
            -  Ontological justification:
                - Assumption driven 
                -  based on prior knowledge about human heights
                - From previous research we know that heights approximate a normal distribtuion
                - This knowledge is independent of the specific data we've collected.
- 	Different distributions
	- Normal (Gaussian) distribution
        - If we know only mean and variance of a bunch of continuous values  
        - 2 params to estimate - mean and standard deviation
    - Generalized linear models
        - Link function
            - f() represents the link function 
            - The link function transforms the linear combination of predictors into a form that ensures the expected value of the response variable is positive 
            -  This transformation is necessary cuz the linear combination can take any real value, but many types of data (counts, probabilities) must be non-negative
            - Different link functions are used for different type of data (Poisson, binomial, gaussian) to ensure appropriate modeling 
            - Link functions: logit and log
                - Logit - binomial
                -  Log - Poisson
        - Application in  distributions
            - Binomial distribution (logistic regression)
                - Max entropy
                    - only 2 things can happen (yes/no, true/false)
                    - there's a constant chance p of each across n trials
                - Use logit link function
            - Beta-Binomial distribution Beta-Binomial(N, p, θ):
                - Used when Binomial cannot be used
                - For instance there is not a constant chance p
                - 2 params to estimate - p and θ
                - Uses logit function 
            - exponential distribution
                - Constrained to be zero or positive.
                - a fundamental distribution of distance and duration
                - Has max entropy among all non-negative continuous distributions with the same average displacement
                - 1 param to estimate - λ (shape)
                - Uses log link
            - Gamma distribution
                - constrained to be zero or positive
                - a fundamental distribution of distance and duration
                - have a peak above zero
            - Poisson distribution
                - Also a count (like Binomials's 0/1) 
                - If the number of trials n is very large (and usually unknown) and the prob. of a success p is very small: Binomial distribution converges to a poisson distribution with an expected rate of events per unit time of λ = np
                - In short, when we have a count going from 0 → ∞ Poisson is a good first choice
                - BUT:
                    - Poisson is picky
                    - We only estimate λ which represents both the mean and the variance so your outcome bar should have equal variance and mean
                    - Very often that is not the case and we fall back Gamma-Poisson
                    - Uses log link function
                -  Gamma-Poisson (λ, φ)  (negative-Binomial(r, p)) 
                    - Uses log link function 
                    - 2 params to model:
                    - We model the variance separately i.e. first λ and then the variance φ
                - Multinomial distribution or Categorical(p)
                    - If we have categories we want to model (red, white, blue)
                    - Used with softmax link function
                - Case of ordered categorical outcomes (and predictors) e.g. Likert scale
## linear regression
- Categorical 
- Alpha : 
    - Interception: 
        - average value
        - represents the expected value of the response variable when all predictor vars are zero
    - Varying intercept
        - that we estimate a unique intercept for each subgroup or category within the dataset
        - help the model to better capture the differences in the data by providing personalized starting points for diff subgroups
- Beta
    - Slope: Coeffective influence for the parameter
    - Varying slopes:
        - Allow us to estimate separate slopes for each subgroup or category within the dataset
        - provide more flexibility in capturing varying relationships within subgroups
- Prior: 
    - Initial belief or knowledge about a parameter before observing new data
- Posterior:
    - Updated belief about the parameter after considering new data
    - posterior probability distribution is proportional to the likelihood times the prior belief
    - End goal with our bayesian analysis
- Information entropy
    - Quantifies the amount of uncertainty associated with the outcomes
    - If there are n diff possible events and each event i has prob. p_i
    - = the uncertainty contained in a probability distribution is the average log-probability of an event

## Multilevel model
- Explain 4 main benefits of using them
    - Improved estimates for repeat sampling
        - Sometimes more than one observation arises from the same individual, location or time
        - Single-level models might either underfit or overfit the data when dealing with related observations
        - Improved estimates for imbalance in sampling
    - Some individuals, locations or times may be sampled more frequently than others, creating an imbalance in the data
        - Multilevel models prevent over-sampled clusters from dominating inference, ensuring that the analysis is not biased towards heavily sampled groups 
    - Estimates of variation:
        - Multilevel mdoels explicitly model variation among individuals or other groups within the data
        - This is beneficial when our research questions include variation among individuals or other groups within the data
    - Avoid averaging, keep variation
        - Scholars often pre-average data to consrtuct variables
        - Averaging removes variation + there are multiple ways to perform averaging
        - Multilevel models enable reseachers to rpeserve the uncertainty and avoid data transformations
    - Rewrite a model to a multilevel model

## 	Do prior check:
- is very useful for assigning sensible priors, because it can be quite hard to anticipate how priors influence the observable variables
- So one thing to always check with a log link is whether it makes sense at all ranges of the predictor variables.
- Regurlarizing prior
    - revise the priors until the prior predictive distribution produced only reasonable outcomes
    - reduce overfitting
- prior predictive simulation
    - Plot the implied line
    - Plot the single prior dentisy

## Diagnosting markov chains
- Trace plot and trank plot
    - Check if the chains are mixing well; 
    - If they are converging around the same parameter space
    - Traceplot with lot of oscillations might indicate a lack of convergence
- effective number of samples: 
    - How many effectively independent samples you have in chain
    - 10% of total sample size (after warmup) or at least 200 for each parameter  
    - Low ESS suggests that the chain is not exploring the parameter space effectively
- R
    - An assessment of the balance between the variance within each chain and the variance between chains
    - Indicates if we have reached a stationary posterior probability distribution 
    - Should approach 1.0 and below 1.01 - suggests convergence (= multiple chains have explored the parameter space consistently)
    - Greater than 1 -> chains have not converged and the results may be unreliable
    - If you draw more iterations, it could be fine, or it could never converge
- MCSE (markov chain standard error) 
    - Estimate of standard error 
    - Can provide a measure of precision of monte carlo approximation 
    - Smaller values indicate a more accurate estimate (of the posterior distribution)

## underfitting & overfitting
- Overfitting
    - Occurs when model is too complex 
    - Happens when there are too many params and limited data
    - Model can fit the training data perfectly but will perform poorly on new data
- Underfitting 
    - Occurs when model is learning too little from the data.
    - Fails to learn the relationships between vars or make accurate predictions
- three principled ways of avoiding overfitting
    - regularizing priors 
        - capture the regular features of data.
    -  scoring device to model the prediction task and estimate predictive accuracy
        -  information theory (entropy and max ent distribution)
        - The concept of information criteria (AIC, LOO, WAIC)
    - Pooling
        - Partial pooling:
            - Params can be estimated with a combination of subgroup-specific info and info shared across all subgroups
            - Allows to find the right balance between capturing group-level patterns and individual variations
            - Choosing appropriate model complexity
        -  Not pooling
            - Each subgroup is analyzed separately
            - Assumes that there are no shared info between subgroups
            - Can lead to overfitting
        - Complete pooling:
            - All data from diff subgroups are combined into a single analysis
            - This approach assumes that all subgroups are identical and there is no variation between them
            - May not accurately capture differences between subgroups
- Compare model
    - WAIC / PSIS:  
        - WAIC - information criteria (the lower the better) 
        - pWAIC - effective number of params in the model
        - dWAIC - the difference in WAIC between the models
        - Weight - an approximate way to indicate which model WAIC prefers
        - SE - standard error. How much noise are we not capturing
        - dSE - difference in standard error between the models
        - If the dWAIC is 4-6 times larger than dSE -> strong indications there is a 'significant' difference

## Do posterior check




    







