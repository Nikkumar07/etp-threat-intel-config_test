#!/bin/bash
d="172.24.80.10"

# Check if the table exists on HDFS
ssh root@"$d" "hdfs dfs -count '/akamai/csi/hdfs_temp/etp-threat-intel-config/*'"
exit_status=$?

if [ $exit_status -ne 0 ]; then
  echo "/akamai/csi/hdfs_temp/etp-threat-intel-config/* table not found on HDFS. Manual intervention required."
  exit 1
fi

ssh root@"$d" "hdfs dfs -count /akamai/csi/hdfs_temp/etp-threat-intel-config/*" | awk '{print $4}' > path.txt
echo "Number of files present:"
cat path.txt | wc -l
file_list=$(cat path.txt | awk '{print $NF}')
deleted_count=0
mismatch=false  # Flag to track if there's a mismatch

# Double-Check if files on HDFS match files before deletion
for i in $file_list; do
  # Check if the file exists on HDFS
  ssh root@"$d" "hdfs dfs -count '$i'"
  #exit_status=$?

  if [ $exit_status -ne 0 ]; then
    echo "File $i does not exist on HDFS. Skipping deletion."
  else
    # Uncomment the following lines to enable file deletion
     echo  "\n  Deleting the file $i... \n"
     ssh root@"$d" "export HADOOP_USER_NAME=hdfs; hdfs dfs -rm -r '$i'"

    if [ $exit_status -eq 0 ]; then
      ((deleted_count++))  # Increment the counter if the file was deleted successfully
    else
     mismatch=true  # Set the flag to true if deletion failed
     break  # Exit the loop early since there's a mismatch
    fi
 fi
done
  echo   "\n  Number of deleted files: $deleted_count \n"
