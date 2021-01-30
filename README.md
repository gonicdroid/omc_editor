# CHANGE cscfeature.xml USING RECOVERY
## A very simple shell script for add lines in the file
It's made to use with AROMA installer, because it reads a prop file (in this case, from /tmp/aroma/omc.prop) to know if add or not the feature line
You can change it to use for your purpose!
<li>Note: make sure to use a UNIX text file, if you use a Windows formatted one, the recovery can't read the script!. You can download the script of this repo to dont fight with that problem.

### The shell script
``` go
#!/sbin/sh
# Written by Gonic

PROPERTY_FILE=/tmp/aroma/omc.prop
omcdir=/tmp/omc
singledir=/tmp/single
lines=/tmp/lines.prop
si=1

getProperty () {
   PROP_KEY=$1
   PROP_VALUE=`cat $PROPERTY_FILE | grep "$PROP_KEY" | cut -d'=' -f2`
   echo $PROP_VALUE
}

# Delete the old prop if exists.
rm -rf $lines

prop=$(getProperty "policy")
feature='<CscFeature_SetupWizard_DisablePrivacyPolicyAgreement>TRUE</CscFeature_SetupWizard_DisablePrivacyPolicyAgreement>'
if [ $prop=$si ];
	then
		echo '   ' $feature >> $lines
fi

prop=$(getProperty "networkspeed")
feature='<CscFeature_Setting_SupportRealTimeNetworkSpeed>TRUE</CscFeature_Setting_SupportRealTimeNetworkSpeed>'
if [ $prop=$si ];
	then
		echo '   ' $feature >> $lines
fi

#For of loop to add the lines to omc folder
for entry in "$omcdir"/*
do
	sed -i '/^$/d' $entry/conf/cscfeature.xml	# Delete blank lines 
	sed -i '$d' $entry/conf/cscfeature.xml	# Delete the last line that ends XML
	sed -i '$d' $entry/conf/cscfeature.xml	# Delete the Features tag
	cat $lines >> $entry/conf/cscfeature.xml # Add the lineas
	echo '  ''</FeatureSet>' >> $entry/conf/cscfeature.xml # Finish the XML tag
	echo '</SamsungMobileFeature>' >> $entry/conf/cscfeature.xml # Finish the XML file again
done

#For of loop to add the lines to single folder
for entry in "$singledir"/*
do
	sed -i '/^$/d' $entry/conf/cscfeature.xml	# Delete blank lines 
	sed -i '$d' $entry/conf/cscfeature.xml	# Delete the last line that ends XML
	sed -i '$d' $entry/conf/cscfeature.xml	# Delete the Features tag
	cat $lines >> $entry/conf/cscfeature.xml # Add the lineas
	echo '  ''</FeatureSet>' >> $entry/conf/cscfeature.xml # Finish the XML tag
	echo '</SamsungMobileFeature>' >> $entry/conf/cscfeature.xml # Finish the XML file again
done
exit 0

# Lines for do the same with cscfeature_network.xml file.
#	sed -i '/^$/d' $entry/conf/cscfeature_network.xml	# Delete blank lines 
#	sed -i '$d' $entry/conf/cscfeature_network.xml	# Delete the last line that ends XML
#	sed -i '$d' $entry/conf/cscfeature_network.xml	# Delete the Features tag
#	echo '  ''</FeatureSet>' >> $entry/conf/cscfeature_network.xml # Finish the XML tag
#	echo '</SamsungMobileFeature>' >> $entry/conf/cscfeature_network.xml # Finish the XML file again
```
### A prop file example:
``` go
policy=0
networkspeed=1
```
### For start it from updater script, u need to extract it first, then give it the permissions and finally run it
For example:
``` go
package_extract_dir("META-INF/scripts", "/tmp");
set_metadata("/tmp/fting.sh", "uid", 0, "gid", 0, "mode", 0777);
run_program("/tmp/fting.sh");
```
