
---

### **۱. مقدمه‌ای بر اوراق خزانه بدون کوپون**

اوراق خزانه بدون کوپون اوراق قرضه‌ای هستند که به سرمایه‌گذاران هیچ‌گونه بهره (کوپون) پرداخت نمی‌کنند. به جای آن، این اوراق با تخفیف از ارزش اسمی خود صادر می‌شوند و در تاریخ سررسید به دارنده‌ی آن، مبلغ اسمی کامل پرداخت می‌شود. سود سرمایه‌گذار از تفاوت بین قیمت خرید اوراق و مبلغ پرداخت شده در سررسید ناشی می‌شود.

**فرمول قیمت‌گذاری اوراق خزانه بدون کوپون:**

![Equation](https://latex.codecogs.com/png.latex?P%20%3D%20%5Cfrac%7BF%7D%7B%281&plus;r%29%5ET%7D)

که در آن:
- \( P \) قیمت اوراق است.
- \( F \) ارزش اسمی اوراق است.
- \( r \) نرخ بازده مورد انتظار (Yield to Maturity) است.
- \( T \) زمان تا سررسید اوراق (بر حسب سال) است.

---

### **۲. مدل‌های واسیچک و CIR در قیمت‌گذاری اوراق**

مدل‌های **واسیچک** و **CIR** دو مدل مشهور در مالی هستند که برای توصیف فرآیندهای تصادفی نرخ بهره استفاده می‌شوند. این مدل‌ها به ما اجازه می‌دهند تا نرخ بهره را به عنوان یک فرآیند تصادفی مدل‌سازی کنیم و از آن برای قیمت‌گذاری اوراق قرضه استفاده کنیم.

#### **۲.۱. مدل واسیچک (Vasicek Model)**

مدل واسیچک یکی از اولین مدل‌های تصادفی نرخ بهره است که توسط فریتز واسیچک در سال ۱۹۷۷ معرفی شد. این مدل فرض می‌کند که نرخ بهره تحت تأثیر میانگین بلندمدت (\( \theta \))، میزان تمایل به بازگشت به این میانگین (\( \kappa \)) و نوسانات (\( \sigma \)) قرار دارد.

**فرمول مدل واسیچک:**

![Vasicek Equation](https://latex.codecogs.com/png.latex?dr_t%20%3D%20%5Ckappa%20%28%5Ctheta%20-%20r_t%29%20dt%20&plus;%20%5Csigma%20dW_t)

که در آن:
- \( r_t \) نرخ بهره در زمان \( t \) است.
- \( \kappa \) نرخ بازگشت به میانگین است.
- \( \theta \) میانگین بلندمدت نرخ بهره است.
- \( \sigma \) نوسانات نرخ بهره است.
- \( dW_t \) نمایانگر فرآیند براونی است.

**قیمت‌گذاری اوراق بدون کوپون با مدل واسیچک:**

![Vasicek Bond Price](https://latex.codecogs.com/png.latex?P%20%3D%20A(T)%20%5Ccdot%20e%5E%7B-B(T)%20r_0%7D)

که در آن:
- \( A(T) \) و \( B(T) \) توابعی هستند که به پارامترهای مدل بستگی دارند.
- \( r_0 \) نرخ بهره اولیه است.

**در کد Streamlit:**

در کلاس `VasicekModel`، متد `analytic_bond_price` این فرمول را پیاده‌سازی می‌کند:

```python
def analytic_bond_price(self, T: float) -> float:
    try:
        kappa = self.params['kappa']
        theta = self.params['theta']
        sigma = self.params['sigma']
        r0 = self.params['X0']

        B_T = (1 - np.exp(-kappa * T)) / kappa
        A_T = np.exp(
            (theta - (sigma**2) / (2 * kappa**2)) * (B_T - T)
            - (sigma**2 / (4 * kappa)) * B_T**2
        )
        P = A_T * np.exp(-B_T * r0)
        return P
    except Exception as e:
        self.logger.error(f"Error calculating analytic bond price at T={T}: {e}")
        return np.nan
```

---

#### **۲.۲. مدل CIR (Cox-Ingersoll-Ross Model)**

مدل CIR که توسط جان سی. کوکس، جان ه. اینگرسول و سورن روس در سال ۱۹۸۵ معرفی شد، مشابه مدل واسیچک است اما ویژگی اضافه‌ای دارد که اجازه می‌دهد نرخ بهره به طور طبیعی به سمت صفر بازگردد، که این امر در مدل واسیچک وجود ندارد.

**فرمول مدل CIR:**

![CIR Equation](https://latex.codecogs.com/png.latex?dr_t%20%3D%20%5Ckappa%20%28%5Ctheta%20-%20r_t%29%20dt%20&plus;%20%5Csigma%20%5Csqrt%7Br_t%7D%20dW_t)

که در آن:
- تمامی نمادها مشابه مدل واسیچک هستند، با این تفاوت که نوسانات با ریشه‌ی مربع نرخ بهره ضریب داده می‌شوند، که باعث می‌شود نرخ بهره نمی‌تواند منفی شود.

**قیمت‌گذاری اوراق بدون کوپون با مدل CIR:**

![CIR Bond Price](https://latex.codecogs.com/png.latex?P%20%3D%20A(T)%20%5Ccdot%20e%5E%7B-B(T)%20r_0%7D)

که در آن:
- \( A(T) \) و \( B(T) \) توابعی هستند که به پارامترهای مدل بستگی دارند.

**در کد Streamlit:**

در کلاس `CIRModel`, متد `analytic_bond_price` این فرمول را پیاده‌سازی می‌کند:

```python
def analytic_bond_price(self, T: float) -> float:
    try:
        kappa = self.params['kappa']
        theta = self.params['theta']
        sigma = self.params['sigma']
        r0 = self.params['X0']

        h = np.sqrt(kappa**2 + 2 * sigma**2)
        numerator = 2 * h * np.exp((kappa + h) * T / 2)
        denominator = (2 * h + (kappa + h) * (np.exp(h * T) - 1))
        A = (numerator / denominator) ** (2 * kappa * theta / sigma**2)
        B = (2 * (np.exp(h * T) - 1)) / (2 * h + (kappa + h) * (np.exp(h * T) - 1))
        P = A * np.exp(-B * r0)
        return P
    except Exception as e:
        self.logger.error(f"Error calculating analytic bond price at T={T}: {e}")
        return np.nan
```

---

### **۳. توضیح فرآیند محاسبه قیمت اوراق خزانه بدون کوپون با ساختار درختی**

برای محاسبه قیمت اوراق خزانه بدون کوپون با استفاده از مدل‌های واسیچک و CIR، فرآیند زیر را دنبال می‌کنیم:

#### **۳.۱. جمع‌آوری و آماده‌سازی داده‌ها**
- **داده‌های موردنیاز:**
  - نرخ بهره تاریخی (برای کالیبراسیون مدل‌ها)
  - اطلاعات اوراق قرضه:
    - ارزش اسمی (Face Value)
    - زمان تا سررسید (Maturity)
    - قیمت بازار (Market Price)
- **اقدامات آماده‌سازی:**
  - پاک‌سازی داده‌ها: حذف مقادیر خالی یا اشتباه.
  - نرمال‌سازی: تبدیل نرخ بهره به مقیاس سالانه (اگر داده‌های روزانه باشد).
  - تبدیل زمان سررسید به سال (مثلاً تبدیل روزها به سال).

#### **۳.۲. انتخاب مدل نرخ بهره**
- **دو مدل اصلی:**
  1. **مدل واسیچک**
  2. **مدل CIR**

#### **۳.۳. کالیبراسیون مدل**
- **هدف:** تخمین پارامترهای مدل (\( \kappa \), \( \theta \), \( \sigma \)).
- **روش‌ها:**
  - حداکثر درست‌نمایی (Maximum Likelihood Estimation - MLE)
  - بهینه‌سازی ترکیبی (Differential Evolution و L-BFGS-B)

#### **۳.۴. محاسبه قیمت اوراق**
- **روش ۱: تحلیل‌گرانه**
  - استفاده از فرمول‌های بسته برای محاسبه \( A(T) \) و \( B(T) \).
  - محاسبه قیمت نهایی با استفاده از این توابع و نرخ بهره اولیه.
- **روش ۲: شبیه‌سازی مونت‌کارلو**
  - تولید مسیرهای تصادفی نرخ بهره.
  - محاسبه مقدار تنزیل‌شده (Discounted Value) برای هر مسیر.
  - میانگین‌گیری از نتایج شبیه‌سازی برای تخمین قیمت.

#### **۳.۵. مقایسه قیمت‌ها**
- **تحلیل:**
  - مقایسه قیمت‌های تحلیل‌گرانه و شبیه‌سازی با قیمت بازار.
  - بررسی تفاوت‌ها برای شناسایی اوراق کم‌ارزش‌گذاری‌شده یا بیش‌ارزش‌گذاری‌شده.

---

### **۴. پیاده‌سازی در کد Streamlit**

در کد ارائه شده، تمامی مراحل فوق به صورت ساختاری و در قالب کلاس‌ها و توابع پیاده‌سازی شده‌اند. در ادامه، توضیحاتی در مورد هر بخش از کد ارائه می‌دهم:

#### **۴.۱. بارگذاری و پردازش داده‌ها**

کلاس `DataLoader` مسئول بارگذاری فایل‌های داده‌های نرخ بهره و اوراق است. این کلاس با استفاده از تابع `map_columns_with_fuzzy_matching` ستون‌های داده‌ها را به نام‌های استاندارد تبدیل می‌کند و اطمینان حاصل می‌کند که تمامی ستون‌های مورد نیاز موجود هستند.

```python
class DataLoader:
    """
    Handles loading and processing of interest rate and bonds data.
    """

    def __init__(self, logger: logging.Logger):
        self.logger = logger

    def load_interest_rate_data(self, uploaded_file) -> Optional[pd.DataFrame]:
        # Implementation as described earlier
        ...

    def load_bonds_data(self, uploaded_file) -> Optional[pd.DataFrame]:
        # Implementation as described earlier
        ...
```

#### **۴.۲. تخمین پارامترهای مدل**

برای هر مدل (واسیچک و CIR)، کلاس‌های `VasicekModel` و `CIRModel` مسئول تخمین پارامترهای \( \kappa \), \( \theta \), \( \sigma \), و \( X0 \) هستند. این تخمین‌ها با استفاده از روش‌های بهینه‌سازی مانند **Differential Evolution** و **MLE (Maximum Likelihood Estimation)** انجام می‌شوند.

```python
class VasicekModel:
    """
    Handles Vasicek model parameter estimation and bond pricing.
    """
    def __init__(self, ir_series: pd.Series, logger: logging.Logger):
        self.ir_series = ir_series
        self.logger = logger
        self.params: Dict[str, Any] = {}

    def estimate_parameters(self) -> Dict[str, Any]:
        # Implementation as described earlier
        ...

    def predict_interest_rate(self, T: float) -> Dict[str, Any]:
        # Implementation as described earlier
        ...

    def analytic_bond_price(self, T: float) -> float:
        # Implementation as described earlier
        ...
```

#### **۴.۳. قیمت‌گذاری اوراق**

پس از تخمین پارامترها، کلاس `BondPricer` با استفاده از متدهای `analytic_bond_price` و `monte_carlo_bond_price`، قیمت اوراق را محاسبه می‌کند. قیمت‌گذاری تحلیل‌گرانه (Analytic Price) با استفاده از فرمول‌های مدل‌های واسیچک و CIR انجام می‌شود، در حالی که قیمت‌گذاری مونت‌کارلو (Monte Carlo Price) با شبیه‌سازی مسیرهای نرخ بهره و محاسبه تخفیف‌های مربوطه انجام می‌پذیرد.

```python
class BondPricer:
    """
    Handles bulk bond pricing using different methods and predicts interest rates at maturity.
    """
    def __init__(self, model, logger: logging.Logger):
        self.model = model
        self.logger = logger

    def price_bond(self, bond: pd.Series) -> Dict[str, Any]:
        # Implementation as described earlier
        ...

    def monte_carlo_bond_price(self, T: float) -> Tuple[float, float]:
        # Implementation as described earlier
        ...

    def price_bulk_bonds(self, bonds_df: pd.DataFrame) -> pd.DataFrame:
        # Implementation as described earlier
        ...
```

#### **۴.۴. توصیه و رتبه‌بندی اوراق**

تابع `rank_bonds` معیارهای مختلف را برای رتبه‌بندی اوراق اعمال می‌کند:
- **YTM_Score:** بالاتر بودن YTM امتیاز بالاتری می‌دهد.
- **Undervaluation_Score:** تفاوت بین قیمت تحلیل‌گرانه و قیمت فعلی.
- **Bid-Ask_Spread_Score:** هرچه اسپرد باربند-آسک باریک‌تر باشد، امتیاز بالاتری.
- **Volume_Score:** حجم معاملات بالاتر، امتیاز بالاتر.
- **CI_Score:** عرض بازه اطمینان هرچه کمتر باشد، امتیاز بالاتری.

این امتیازات نرمالیزه شده و با وزن‌های قابل تنظیم توسط کاربر ترکیب می‌شوند تا امتیاز کلی هر اوراق تعیین شود و بر اساس این امتیازها، اوراق رتبه‌بندی می‌شوند.

```python
def rank_bonds(priced_bonds_df: pd.DataFrame, logger: logging.Logger) -> pd.DataFrame:
    """
    Ranks bonds based on specified criteria.
    """
    try:
        df = priced_bonds_df.copy()

        # Ensure required columns are present
        required_columns = [
            'bond_name',
            'Expected Interest Rate at Maturity',
            'Analytic Price',
            'current_price',
            'bid_quote',
            'ask_quote',
            'volume',
            'Std Dev of Interest Rate at Maturity'
        ]
        missing_cols = [col for col in required_columns if col not in df.columns]
        if missing_cols:
            logger.error(f"Missing columns for ranking: {missing_cols}")
            st.error(f"Cannot rank bonds due to missing columns: {missing_cols}")
            return df

        # Calculate individual criterion scores
        # 1. Yield to Maturity (Expected Interest Rate at Maturity)
        df['YTM_Score'] = df['Expected Interest Rate at Maturity']

        # 2. Undervaluation Score (Analytic Price - Current Price)
        df['Undervaluation'] = df['Analytic Price'] - df['current_price']
        df['Undervaluation_Score'] = df['Undervaluation']

        # 3. Bid-Ask Spread Score
        df['Bid_Ask_Spread'] = df['ask_quote'] - df['bid_quote']
        df['Bid_Ask_Spread_Score'] = -df['Bid_Ask_Spread']  # Negative because narrower spread is better

        # 4. Volume Score
        df['Volume_Score'] = df['volume']

        # 5. Confidence Interval Width Score
        df['CI_Width'] = df['Upper Confidence Interval'] - df['Lower Confidence Interval']
        df['CI_Score'] = -df['CI_Width']  # Negative because narrower interval is better

        # Normalize scores between 0 and 1
        score_columns = ['YTM_Score', 'Undervaluation_Score', 'Bid_Ask_Spread_Score', 'Volume_Score', 'CI_Score']
        for col in score_columns:
            min_val = df[col].min()
            max_val = df[col].max()
            if max_val - min_val != 0:
                df[col] = (df[col] - min_val) / (max_val - min_val)
            else:
                df[col] = 0.5  # Assign a neutral score if no variation
            logger.info(f"Normalized {col}")

        # Allow users to adjust weights
        st.sidebar.subheader("5. Adjust Criteria Weights")
        ytm_weight = st.sidebar.slider("Yield to Maturity Weight", min_value=0.0, max_value=1.0, value=0.2, step=0.05)
        undervaluation_weight = st.sidebar.slider("Undervaluation Weight", min_value=0.0, max_value=1.0, value=0.2, step=0.05)
        spread_weight = st.sidebar.slider("Bid-Ask Spread Weight", min_value=0.0, max_value=1.0, value=0.2, step=0.05)
        volume_weight = st.sidebar.slider("Volume Weight", min_value=0.0, max_value=1.0, value=0.2, step=0.05)
        ci_weight = st.sidebar.slider("Confidence Interval Weight", min_value=0.0, max_value=1.0, value=0.2, step=0.05)

        # Ensure the weights sum to 1
        total_weight = ytm_weight + undervaluation_weight + spread_weight + volume_weight + ci_weight
        if total_weight == 0:
            st.error("At least one weight must be greater than zero.")
            return df
        ytm_weight /= total_weight
        undervaluation_weight /= total_weight
        spread_weight /= total_weight
        volume_weight /= total_weight
        ci_weight /= total_weight

        # Calculate overall score
        df['Overall_Score'] = (
            ytm_weight * df['YTM_Score'] +
            undervaluation_weight * df['Undervaluation_Score'] +
            spread_weight * df['Bid_Ask_Spread_Score'] +
            volume_weight * df['Volume_Score'] +
            ci_weight * df['CI_Score']
        )

        # Rank the bonds
        df['Rank'] = df['Overall_Score'].rank(ascending=False, method='min').astype(int)

        # Sort by rank
        df = df.sort_values(by='Overall_Score', ascending=False)

        # Select columns to display
        display_columns = [
            'Rank',
            'bond_name',
            'Overall_Score',
            'Expected Interest Rate at Maturity',
            'Analytic Price',
            'current_price',
            'Undervaluation',
            'Bid_Ask_Spread',
            'volume',
            'CI_Width'
        ]

        return df[display_columns]
    ```

---

### **۵. نتیجه‌گیری**

با ترکیب مدل‌های مالی دقیق مانند واسیچک و CIR با ابزارهای تحلیلی و بصری پیشرفته مانند Streamlit، شما می‌توانید تصمیمات سرمایه‌گذاری هوشمندانه‌تری بگیرید. این سیستم نه تنها قیمت اوراق را محاسبه می‌کند بلکه با استفاده از معیارهای متنوع، اوراق را رتبه‌بندی و بهترین گزینه‌ها را به شما معرفی می‌کند.

**اگر سوال یا نیاز به توضیحات بیشتری دارید، خوشحال می‌شوم که کمک کنم!** 📊💼
