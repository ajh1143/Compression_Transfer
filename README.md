# File Compression Transfer

## Big Picture:
- Define Source and Destination File Locations
- Zip raw data files
- Generate hash prior to transfer
- Generate hash after transfer 
- Compare hashes    
    If hashes match: Unzip Files    
    Else: Error 

```powershell
$localPath = "YOUR_SOURCE_PATH"
$destinationPath = "YOUR_DESTINATION_PATH"


# COMPRESS FILES TO ZIP
function Compress_Files{

    Compress-Archive -Path $localPath -DestinationPath $localPath'Compressed_Data' -compressionlevel optimal
}


# Set Hash of Data payload prior to bulk transfer of compressed data
function HashPriori{
    $sourceHash = (Get-FileHash -Path $localPath'Compressed_Data.zip').Hash
    return $sourceHash
}


# Set Hash of Data payload after to bulk transfer of compressed data
function HashPosteriori{
    $destinationHash = (Get-FileHash -Path $destinationPath'Compressed_Data.zip').Hash
    return $destinationHash
}


function CompareHashes{
    param($sourceHash, $destinationHash)
    if ($sourceHash.HashString -eq $destinationHash.HashString){
        Write-Output 'Success: Transferred data hash matches source hash'
        Write-Output 'Unzipping Files....'
        Unzip_Files
    }
    else{
        Write-Output 'Error: Hashes do not matches, transfer zip again'
    }
}

function Unzip_Files{
    expand-archive -path $localPath'Compressed_Data.zip' -destinationpath $destinationPath\raw_data
    }

Compress_Files
$hash1 = HashPriori
Write-Output Source $hash1
$hash2 = HashPosteriori
Write-Output Destination $hash2
CompareHashes($hash1, $hash2)
```
