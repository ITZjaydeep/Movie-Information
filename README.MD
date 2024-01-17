$$\\theta_{\text{new}} = \theta_{\text{old}} - \alpha \cdot \nabla J(\theta_{\text{old}})\$$

$$\\theta_{\text{new}} = \theta_{\text{old}} - \alpha \cdot \nabla J(\theta^{(i)})\$$

$$\\theta_{\text{new}} = \theta_{\text{old}} - \alpha \cdot \nabla J(\theta_{\text{old}}, \text{Mini-Batch})\$$

$$\\frac{\partial J(\theta)}{\partial \theta_0} = \frac{1}{m}\sum_{i=1}^{m}(h_{\theta}(x^{(i)}) - y^{(i)})$$

$$\\frac{\partial J(\theta)}{\partial \theta_1} = \frac{1}{m}\sum_{i=1}^{m}[(h_{\theta}(x^{(i)}) - y^{(i)}) \cdot x_1^{(i)}]\$$

$$\\frac{\partial J(\theta)}{\partial \theta_k} = \frac{1}{m}\sum_{i=1}^{m}[(h_{\theta}(x^{(i)}) - y^{(i)}) \cdot x_k^{(i)}]$$

$$\\theta_{\text{new}} = \theta_{\text{old}} - \alpha \cdot \nabla J(\theta_{\text{old}}, \text{Mini-Batch})\$$
