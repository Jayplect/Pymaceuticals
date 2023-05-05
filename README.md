## Desciption
In a laboratory study, 249 mice who were identified with SCC tumors received treatment with a range of drug regimens. Over the course of 45 days, tumor development was observed and measured. The purpose of this project was to compare the performance of Pymaceuticals’ drug of interest, Capomulin, against the other treatment regimens. I then generated all of the tables and figures needed for the technical report of the clinical study as well as provided a top-level summary of the study results.

## Dependencies used
<img width="120" src = https://user-images.githubusercontent.com/107348074/236379504-0f0e8534-6435-4924-b72d-283946e03c4b.png>
<img width="120" src = https://user-images.githubusercontent.com/107348074/236379877-e0e3b90e-217f-4700-ade2-df8b5ef8f23b.png>
<img width="120" src =https://user-images.githubusercontent.com/107348074/236379730-0286f397-c9e0-4e0c-a91c-e07d64f6ceec.png>
<img width="120" src = https://user-images.githubusercontent.com/107348074/236379825-80dc02bc-46c1-46fa-9634-dc28cdcb5704.png>

## Summary of Dataset
Two sets of data are provided for this project. One of the the datasets includes the metadata for each mouse while the other includes the study results from. The data used in this analysis were simulated for the purpose of this project.

## Project Steps
### Step 1: Merging both data sets 
- The first step after loading both data sets was to use an outer join from pandas to merge both data sets on 'Mouse ID'  
  Load and peruse data files
  
      mouse_metadata_path = "data/Mouse_metadata.csv"
      study_results_path = "data/Study_results.csv"

  Read the mouse data and the study results
  
      mouse_metadata = pd.read_csv(mouse_metadata_path)
      study_results = pd.read_csv(study_results_path)

  Combine the data into a single DataFrame
  
      combined_df = pd.merge(mouse_metadata, study_results, on = 'Mouse ID', how ='outer' )

- Our data should be uniquely identified by Mouse ID and Timepoint. To be sure there are no duplicates, I checked for any duplicates using these unique IDs i.e., Mouse ID and Timepoint.  
      
      duplicate_mouse_ID = combined_df.loc[combined_df.duplicated(subset = ['Timepoint','Mouse ID']), 'Mouse ID'].unique()
      duplicate_mouse_ID
      
- Create a clean DataFrame by dropping the duplicate mouse by its ID.  

      clean_df = combined_df.loc[combined_df['Mouse ID'] != 'g989', :]

### Step 2: Summary Statistics
- I generated a summary statistics table of mean, median, variance, standard deviation, and SEM of the tumor volume for each drug regimen. The aggregation function made this easy.
      
      clean_df.groupby(["Drug Regimen"]).agg(["mean","median","var","std","sem"])["Tumor Volume (mm3)"]

### Step 3: Visualizations
- Generate a bar plot showing the total number of rows (Mouse ID/Timepoints) for each drug regimen using pyplot. 

      drug_count = clean_df["Drug Regimen"].value_counts()

      plt.figure(figsize=(9.5,7.5))
      plt.bar(drug_count.index, drug_count.values)
      plt.title("Number of Mice per Drug Regimen", size = 24)
      plt.xlabel("Drug Regimen", size =16)
      plt.ylabel("# of Observed Mice", size = 16)
      plt.ylim(0, max(clean_df["Drug Regimen"].value_counts() + 20))
      plt.xticks(rotation="vertical", fontsize=14)
      plt.yticks(fontsize=14)

      plt.show()
      
    <img width  = "350" src =https://user-images.githubusercontent.com/107348074/236381933-6c8375cc-00fc-4f70-a3e7-98c6eed9f780.png>
   
- Generate a box plot that shows the distrubution of the tumor volume for each treatment group.  
      
      fig1, ax1 = plt.subplots()
      fig = ax1.boxplot(tumor_vol_data, labels=treatments, \
                        flierprops = dict(marker = "o",markersize=10, markerfacecolor = "red"))
      ax1.set_title('Distrubution of tumor volume')
      ax1.set_ylabel('Final Tumor Volume (mm3)')
      ax1.set_xlabel('Drug Regimen')
      plt.show()
    <img width  = "350" src =https://user-images.githubusercontent.com/107348074/236382918-97b88038-0a61-4143-a108-dc311a03cb55.png>

- Calculate the correlation coefficient and a linear regression model for mouse weight and average observed tumor volume for the entire Capomulin regimen.  

      correlation = st.pearsonr(avg_mouse_weight['Weight (g)'],avg_mouse_weight['Tumor Volume (mm3)'])
      print(f"The correlation between mouse weight and the average tumor volume is {round(correlation[0],2)}")

    regression line 
 
      (slope, intercept,rvalue, pvalue, stderr)= linregress(avg_mouse_weight["Weight (g)"],avg_mouse_weight["Tumor Volume (mm3)"])
      regress_values=avg_mouse_weight["Weight (g)"]* slope + intercept
      line_eq= f"y = {round(slope, 2)} x + {round(intercept, 2)}"
      print(f"The r-squared is: {round(rvalue**2, 2)}")

    plot regression line and scatter plot  
      
      plt.figure(figsize=(8,6))
      plt.xticks(fontsize=12)
      plt.yticks(fontsize=12)
      plt.scatter(avg_mouse_weight["Weight (g)"],avg_mouse_weight["Tumor Volume (mm3)"])
      plt.plot(avg_mouse_weight["Weight (g)"], regress_values, color='red')
      plt.annotate(line_eq,(20,36), fontsize=14)
      plt.title('Capomulin: Mouse weight vs. Average tumor volume', size =20)
      plt.xlabel('Average tumor volume', size =16)
      plt.ylabel('Mouse Weight (g)', size =16)
      plt.show()
    <img width  = "350" src =https://user-images.githubusercontent.com/107348074/236382973-b90e0855-8b5c-424e-ad40-bff8ccb04224.png>  
## Summary of Results 
- Firstly, Capomulin, our drug of interest, and Ramicane had higher number application per mice compared to Infubinol and Ceftamin. Propriva was administered the least per mice. Further, boxplot analysis showed that both Capomulin and Ramicane regimens had the most effect on the mice tumor volume and were not statistically different from each other (pvalue = 0.769).  
- Secondly, There is a strong positive correlation (0.84) between the mouse weight and the average tumor volume for the entire Capomulin regimen. This correlation seemed to suggest that for every average tumor decline in mouse, there was an accompanied weight loss and vice versa. The pie chart revealed gender distribution was even.  
- Lastly, from the scatter plot, the regressed line between mouse weight and the average tumor volume for Capomulin could be used to model for variations in tumor volume based on changes in mouse weight with an r-squared of 0.71.  

## References
Data for this dataset was generated by edX Boot Camps LLC, and is intended for educational purposes only.
