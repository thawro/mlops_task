# Task description

You will implement whole ML pipeline with DVC tool combined with Docker. The target is to estimate blood pressure (systolic and diastolic blood pressure) values using PPG data. Read all points before you start and ask questions if something is not clear. Good luck!

# Data description

The data you will use consists of PPG (Photopletysmograph) and ABP (Arterial Blood Pressure) signals. PPG is stored in `data/ppg.npy` and ABP is stored in `data/abp.npy`. PPG signals serves as source of input features to ML model and ABP serves as source of ground truth values for ML model. The signals are 120s long (15000 samples, sampling frequency = 125Hz).

# Task

## 0. Clone repository and create your branch.

## 1. Implement ML pipeline using [DVC pipelines](https://dvc.org/doc/start/data-management/pipelines).

The pipeline is supposed to consist of five stages:

1. Extract features. We can provide the script for that step if you wish. 
    * Use `data/ppg.npy` as source of PPG data and `data/abp.npy` as source of ABP data
    * features from PPG: features like `mean`, `std`, `kurtosis`, etc.
    * labels from ABP data: 
        * systolic blood pressure (`sbp`) - mean value of ABP local maximas ([find_peaks](https://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.find_peaks.html) recommended)
        * diastolic blood pressure (`dbp`) - mean value of ABP local minimas ([find_peaks](https://docs.scipy.org/doc/scipy/reference/generated/scipy.signal.find_peaks.html) recommended)
2. Split data into `train` and `test` datasets. Use data output from #1. Must be dependent on `train_size` param used to define size of train data after the split.
3. Preprocess data with [StandardScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html). Use data output from #2. Must be dependend on `use_scaler` param used to define if the data will be scaled in that step.
4. Fit ML model (of your choice). Use data output from #3. Must be dependent on `target` param used to define if labels for ML model will be systolic blood pressure (`sbp`) or diastolic blood pressure (`dbp`) 
5. Evaluate ML model from #4. Use data output from #3. Must be dependent on `target` (same as in #4) param used to define if labels for evaluation will be systolic blood pressure (`sbp`) or diastolic blood pressure (`dbp`) 
    * Report evaluation metrics (`mae` and `mse`)
    * Plot results (`y_pred` vs `y_test`)
    * Save metrics and plots to files.
    
## 2. Use `docker compose` to containerize the dvc pipeline.

Make it possible for us to run the whole application with `docker compose up`. Container is supposed to be running, so we can enter its `bash` with `docker exec -it container_id bash` and run dvc experiments from within the container. 

After running dvc pipeline (`dvc repro`) `results` directory is supposed to contain at least:
* `metrics.json` file with evaluation metrics
* `pred_vs_true.jpg` file with results figure

## 3 Add [MLflow](https://www.mlflow.org/docs/latest/python_api/mlflow.html) for metrics and params logging. 

You can add MLflow using one of 3 options (your choice):
* Add MLflow logging to the same service as DVC (not recommended)
* Create new (mlflow) service with some volumes shared with DVC service
* Create new (mlflow) service which will use some remote bucket as source of experiments info (DVC service is supposed to log metrics to that bucket) 
