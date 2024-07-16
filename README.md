Select *
From SQLTutorial.dbo.NashvilleHousing

Select SaleDateConverted, CONVERT(Date,SaleDate)
From SQLTutorial.dbo.NashvilleHousing

Update NashvilleHousing
SET SaleDate = CONVERT(Date,SaleDate)

ALTER TABLE NashvilleHousing
Add SaleDateConverted Date;

Update NashvilleHousing
SET SaleDateConverted = Convert (Date,SaleDate)


Select SaleDate, CONVERT(Date,SaleDate)
From SQLTutorial.dbo.NashvilleHousing

--Populate Property Address Data --------------------------------------------------------------------

Select *
from NashvilleHousing
--where propertyaddress is null
order by ParcelID

Select a.ParcelID, a.PropertyAddress, b.ParcelID, b.PropertyAddress, ISNULL(a.PropertyAddress,b.PropertyAddress)
from NashvilleHousing a
JOIN NashvilleHousing b 
on a.ParcelID = b.ParcelID
AND a.[UniqueID] <> b.[UniqueID]
Where a.PropertyAddress is null

Update a
set PropertyAddress = ISNULL(a.PropertyAddress,b.PropertyAddress)
from NashvilleHousing a
JOIN NashvilleHousing b 
on a.ParcelID = b.ParcelID
AND a.[UniqueID] <> b.[UniqueID]
Where a.PropertyAddress is null

--Breaking out Address into Individual Columns--------------------------------------
Select PropertyAddress
from NashvilleHousing

SELECT 
SUBSTRING (PropertyAddress, 1, CHARINDEX(',',PropertyAddress)-1) as Address
, SUBSTRING (PropertyAddress, CHARINDEX(',',PropertyAddress)+1, LEN(PropertyAddress)) as Address
from NashvilleHousing



ALTER TABLE NashvilleHousing
Add PropertySplitAddress Nvarchar(255);

Update NashvilleHousing
SET PropertySplitAddress = SUBSTRING (PropertyAddress, 1, CHARINDEX(',',PropertyAddress)-1) 


ALTER TABLE NashvilleHousing
Add PropertySplitCity Nvarchar(255);

Update NashvilleHousing
SET PropertySplitCity = SUBSTRING (PropertyAddress, CHARINDEX(',',PropertyAddress)+1, LEN(PropertyAddress)) 

Select *
from NashvilleHousing

--More Columns ------------------------------------------------------------------
SELECT OwnerAddress
From NashvilleHousing

SELECT
PARSENAME(REPLACE(OwnerAddress,',','.'), 3)
,PARSENAME(REPLACE(OwnerAddress,',','.'), 2)
,PARSENAME(REPLACE(OwnerAddress,',','.'), 1)
From NashvilleHousing

ALTER TABLE NashvilleHousing
Add OwnerSplitAddress Nvarchar(255);

Update NashvilleHousing
SET OwnerSplitAddress = PARSENAME(REPLACE(OwnerAddress,',','.'), 3)


ALTER TABLE NashvilleHousing
Add OwnerSplitCity Nvarchar(255);

Update NashvilleHousing
SET OwnerSplitCity = PARSENAME(REPLACE(OwnerAddress,',','.'), 2)

ALTER TABLE NashvilleHousing
Add OwnerSplitState Nvarchar(255);

Update NashvilleHousing
SET  OwnerSplitState = PARSENAME(REPLACE(OwnerAddress,',','.'), 1)

SELECT *
from NashvilleHousing

--Changing to Yes and No in "SoldasVacant" --------------------------------------
ALTER TABLE  NashvilleHousing
ALTER COLUMN SoldAsVacant VARCHAR(3);

UPDATE NashvilleHousing
SET SoldAsVacant = CASE
    WHEN SoldAsVacant = '1' THEN 'Yes'
    WHEN SoldAsVacant = '0' THEN 'No'
    ELSE SoldAsVacant
END;

Select Distinct (SoldAsVacant), Count(SoldAsVacant)
from NashvilleHousing
Group by SoldAsVacant
order by SoldAsVacant

--Remove Duplicates --------------------------------------------------------------

WITH RowNumCTE as(
Select *, 
ROW_NUMBER() OVER(
PARTITION BY ParcelID, PropertyAddress, SalePrice, SaleDate, LegalReference 
Order by UniqueID) row_num

from NashvilleHousing
--Order by ParcelID
)
Select *
from RowNumCTE
Where row_num >1
order by PropertyAddress


Select *
from NashvilleHousing

--Delete Unused columns -------------------------------
Select *
from NashvilleHousing

ALter Table NashvilleHousing
DROP COLUMN OwnerAddress, TaxDistrict,PropertyAddress

ALter Table NashvilleHousing
DROP COLUMN SaleDate

--Round Acreage 
Select *
from NashvilleHousing

UPDATE NashvilleHousing
SET acreage = ROUND(acreage, 2);


SELECT ROUND(acreage, 2) AS acreage
FROM NashvilleHousing;
