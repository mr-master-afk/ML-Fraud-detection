# ML-Fraud-detection

## პროექტი - IEEE-CIS Fraud Detection
- კონკურსის მიზანი იყო შემოსულ მონაცემებზე დაგვედგინა ესა თუ ის ტრანზაქაცია თაღლითობა იყო თუ არა. მთავარი გამოწვევა ტრენინგის კი იყო დაუბალანსირებული მონაცემები სადაც "Fraud" label-ი დაახლოებით 4%-მდე იყო.
- პროექტზე მუშაობისას გამოვიყენე LogisticRegression და XGBoost(რაც მოვასწარი). ვცვლიდი როგორც პარამეტრების რაოდენობას ასევე ჰიპერპარამეტრებს.

## რეპოზიტორიის სტრუქტურა
- **model-experiment-logistic-regression.ipynb** - LogisticRegression-ზე დატრენინგებული მოდელი თავისი Cleaning, Feature Engineering, Feature Selection და Training მიდგომებით.
- **model-experiment-xgboost.ipynb** - XGBoost-ზე დატრენინგებული მოდელი თავისი Cleaning, Feature Engineering, Feature Selection და Training მიდგომებით.
- **model-inference.ipynb** - საუკეთესო მოდელზე გატესტილი test set-ი.
- **submission.csv** - kaggle-ზე შესაფასებლად გამოყენებული test set-ზე prediction-ები.

## Feature Engineering
- **Cleaning** - მაღალი nan-ის მქონე სვეტები დავდროპე, რომელსაც threshold-ით ვაკონტროლებდი და დანარჩენებში რაც დავიტოვე მაგათი missing value-ები კატეგორიულისთვის შევავსე მოდათი, ხოლო რიცხვითებისთვის მედიანათი. ორივე მოდელში თავიდან 80-85%-ების მქონე სვეტებს ვაგდებდი, ბოლოს კი xgboost-ზე 95% ავიღე, რამაც უკეთესი შედეგი მომცა საბოლოოდ. ეს კი გამოიწვია იმან რომ გადაგდებული სვეტები ინფორმაციას ინახავდნენ და bias შევამცირე მაგით.
- **Dealing with Categorical Columns** - კატეგორიულები თავიდან ვიფიქრე one-hot encoding გამომეყენებინა, მაგრამ ზოგიერთ სვეტს ბევრი მნიშვნელობა ჰქონდა კატეგორიებად და ეს ტრენინგის დროს საკმაოდ გაზრდიდა, ამიტომ ვარჩიე LabelEncoding-ი, რომელიც კატეგორიებს ნომრავდა და ერთ სვეტში აგროვებდა.

## Feature Selection
- Logistic Regression-სთვისაც და XGBoost-სთვისაც გამოვიყენე კორელაცია და მაგის მიხედვით ვარჩევდი საუკეთესოებს. ძირითადად რაოდენობას ვცვლიდი XGBoost-სთვის დაწყებული 200დან 400-მდე.

## Training
- დავატრენინგე **Logistic Regression** და **XGBoost**.
- **Logistic Regression**-სთვის default-ად გავუშვი და მერე იტერაცია და solver-ი შევუცვალე. **XGBoost**-სთვის ვუცვალე: learning-rate, n_estimators, max_depth, scale_pos_weight.
- საბოლოო მოდელი კი შევარჩიე roc_auc_score-ით, ვინაიდან დაუბალანსირებული მონაცემები იყო ყველაზე სანდო შეფასების კრიტერიუმი roc იყო ვიდრე რომელიმე სხვა მეტრიკა.

## MLflow Tracking
- ბმული: https://dagshub.com/mr-master-afk/ML-Fraud-detection.mlflow/#/experiments/0?searchFilter=&orderByKey=params.%60roc_auc_score%60&orderByAsc=true&startTime=ALL&lifecycleFilter=Active&modelVersionFilter=All+Runs&datasetsFilter=W10%3D
- მეტრიკებიდან გამოვიყენე roc_auc_score 
- საბოლოოდ ყველაზე კარგი შედეგი XGBoost-მა მომცა  0.836641 kaggle-ზე. ტრენინგისას train და test set-ზე შედეგებმა თავიდან overfit-ი აჩვენა ვინაიდან 0.93 და 0.86 იყო შედეგი, მაგრამ მერე შედარებით გავაუმჯობესე ჰიპერპარამეტრების ოპტიმიზაციით და პარამეტრების შერჩევით, რასაც კორელაციის ფილტრით ვშვებოდი. საბოლოო ვერსიამ 0.94(train_set) და 0.89(test_set) შედეგები მომცა, რაც მცირე, მაგრამ შედარებით შემცირდა overfit-ი.