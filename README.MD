# Machine Learning

## Linear Regression

- Linear regression is a statistical method.
- It is used to model the relationship between a dependent variable and one or more independent variables.
- It assumes a linear relationship between the variables and aims to find the best-fit line that minimizes the difference between the observed and predicted values.

### Assumptions of Linear Regression

1. Linearity: The relationship between the dependent and independent variables is linear.
2. Independence: Residuals (the differences between observed and predicted values) are independent of each other.
3. Homoscedasticity: Residuals have constant variance across all levels of the independent variables.
4. Normality of residuals: Residuals are normally distributed.
5. No or little multicollinearity: Independent variables are not highly correlated.

### Simple Linear Regression

$$ Y = β₀ + β₁X₁ + ε $$

$$ Ŷ = \hat{\beta₀} + \hat{\beta₁}X₁ $$

$$ β₁ = \frac{\sum_{i=1}^{n} (x_i - \bar{x})(y_i - \bar{y})}{\sum_{i=1}^{n} (x_i - \bar{x})^2} $$

$$ β₀ = \bar{y} - β₁ \bar{x} $$

### Multiple Linear Regression

$$ Y = β₀ + β₁X₁ + β₂X₂ + β₃X₃ + ... + βₖXₖ + ε $$

$$ Ŷ = \hat{\beta₀} + \hat{\beta₁}X₁ + \hat{\beta₂}X₂ + \hat{\beta₃}X₃ + ... + \hat{\betaₖ}Xₖ $$

$$ Y = Xβ + ε $$

$$ Ŷ = X\hat{\beta} $$

$$ \hat{\beta} = (XᵀX)⁻¹XᵀY $$

### GD

$$ Y = β₀ + β₁X₁ + β₂X₂ + β₃X₃ + ... + βₖXₖ + ε $$

$$ h_\theta(X) = \theta_0 + \theta_1X_1 + \theta_2X_2 + \theta_3X_3 + \ldots + \theta_kX_k $$

$$ J(\theta) = \frac{1}{2m}\sum_{i=1}^{m}(h_{\theta}(x^{(i)}) - y^{(i)})^2 $$

$$ \nabla J(\theta) = \begin{bmatrix}
\frac{\partial J(\theta)}{\partial \theta_0} \\
\frac{\partial J(\theta)}{\partial \theta_1} \\
\ ... \\
\frac{\partial J(\theta)}{\partial \theta_k} \\
\end{bmatrix}
$$


### Regression Analysis

### Regularization
