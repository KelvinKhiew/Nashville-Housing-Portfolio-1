# Nashville-Housing-Portfolio-1
Cleaning Data In SQL

--/****** Script for SelectTopNRows command from SSMS  ******/
--SELECT TOP (1000) [UniqueID ]
--      ,[ParcelID]
--      ,[LandUse]
--      ,[PropertyAddress]
--      ,[SaleDate]
--      ,[SalePrice]
--      ,[LegalReference]
--      ,[SoldAsVacant]
--      ,[OwnerName]
--      ,[OwnerAddress]
--      ,[Acreage]
--      ,[TaxDistrict]
--      ,[LandValue]
--      ,[BuildingValue]
--      ,[TotalValue]
--      ,[YearBuilt]
--      ,[Bedrooms]
--      ,[FullBath]
--      ,[HalfBath]
--  FROM [Portfolio Project].[dbo].[NashvilleHousing]

  /*
  Cleaning Data in SQL Queries
  */

  Select*
  From [Portfolio Project]..NashvilleHousing

--Standardize Date Format

  Select SaleDateCOnverted, CONVERT(Date,SaleDate)
  From [Portfolio Project]..NashvilleHousing

  Update NashvilleHousing
  SET SaleDate = CONVERT(Date,SaleDate)

  ALTER TABLE NashvilleHousing
  Add SaleDateConverted Date;

  Update NashvilleHousing
  SET SaleDateConverted = CONVERT(date,SaleDate)

  --Populate Property Address data

  SELECT *
  FROM [Portfolio Project]..NashvilleHousing
  --WHERE PropertyAddress is null
  ORDER BY ParcelID 

  SELECT a.ParcelID, a.PropertyAddress, b.ParcelID, b.PropertyAddress, ISNULL(a.PropertyAddress,b.PropertyAddress)
  FROM [Portfolio Project]..NashvilleHousing a
  JOIN [Portfolio Project].dbo.NashvilleHousing b
  ON a.ParcelID = b.ParcelID
  AND a.[UniqueID ] <> b.[UniqueID ]
  Where a.PropertyAddress is null

  Update a
  SET PropertyAddress = ISNULL(a.PropertyAddress,b.PropertyAddress)
  FROM [Portfolio Project]..NashvilleHousing a
  JOIN [Portfolio Project].dbo.NashvilleHousing b
  ON a.ParcelID = b.ParcelID
  WHERE a.PropertyAddress is null




--Breaking out Address Into Individual Columns (Address, City, State)

  SELECT PropertyAddress
  FROM [Portfolio Project]..NashvilleHousing
  --WHERE PropertyAddress is null
  --ORDER BY ParcelID 

  SELECT
  SUBSTRING(PropertyAddress, 1, CHARINDEX(',',PropertyAddress)-1) as Address
  ,SUBSTRING(PropertyAddress, CHARINDEX(',',PropertyAddress)+1, LEN(PropertyAddress)) as Address
  FROM [Portfolio Project]..NashvilleHousing

  Update NashvilleHousing
  SET PropertySplitAddress =  SUBSTRING(PropertyAddress, 1, CHARINDEX(',',PropertyAddress)-1)

  ALTER TABLE NashvilleHousing
  Add PropertySplitAddress Nvarchar(255);

    Update NashvilleHousing
  SET PropertySplitCity = SUBSTRING(PropertyAddress, CHARINDEX(',',PropertyAddress)+1, LEN(PropertyAddress))

  ALTER TABLE NashvilleHousing
  Add PropertySplitCity Nvarchar(255);


  SELECT *
  FROM [Portfolio Project]..NashvilleHousing

  SELECT OwnerAddress
  FROM [Portfolio Project]..NashvilleHousing



  SELECT
  PARSENAME(REPLACE(OwnerAddress,',','.'),3)
  ,PARSENAME(REPLACE(OwnerAddress,',','.'),2)
  ,PARSENAME(REPLACE(OwnerAddress,',','.'),1)
  FROM [Portfolio Project]..NashvilleHousing





    Update NashvilleHousing
  SET OwnerSplitAddress =  PARSENAME(REPLACE(OwnerAddress,',','.'),3)

  ALTER TABLE NashvilleHousing
  Add OwnerSplitAddress Nvarchar(255);

    Update NashvilleHousing
  SET OwnerSplitCity = PARSENAME(REPLACE(OwnerAddress,',','.'),2)

  ALTER TABLE NashvilleHousing
  Add OwnerSplitCity Nvarchar(255);

      Update NashvilleHousing
  SET OwnerSplitState = PARSENAME(REPLACE(OwnerAddress,',','.'),1)

  ALTER TABLE NashvilleHousing
  Add OwnerSplitState Nvarchar(255);


  SELECT *
  FROM [Portfolio Project]..NashvilleHousing

 -- Change Y and N to Yes and No in "Sold as Vacant" field

 SELECT DISTINCT(SoldAsVacant), COUNT(SoldAsVacant)
 FROM [Portfolio Project]..NashvilleHousing
 GROUP BY SoldAsVacant
 ORDER BY 2

 SELECT SoldAsVacant
 ,CASE WHEN SoldAsVacant = 'Y' THEN 'YES'
 WHEN SOldAsVacant = 'N' THEN 'No'
 ELSE SoldAsVacant
 END
 FROM [Portfolio Project]..NashvilleHousing

 UPDATE NashvilleHousing
 SET SoldAsVacant = CASE WHEN SoldAsVacant = 'Y' THEN 'YES'
 WHEN SOldAsVacant = 'N' THEN 'No'
 ELSE SoldAsVacant
 END





 -- Remove Duplicate

 WITH ROwNumCTE AS(
 SELECT *,
 ROW_Number() OVER (
 PARTITION BY ParcelID,
 PropertyAddress,
 SalesPrice,
 SaleDate,
 LegalReference
 ORDER BY
 UniqueID
 ) row_num

 FROM [Portfolio Project]..NashvilleHousing
 --ORDER BY ParcelID
 )
 FROM RowNUmCTE
 WHERE row_num > 1
 ORDER BY PropertyAddress


 SELECT *
 FROM [Portfolio Project]..NashvilleHousing









 --Delete Unused Columns

SELECT *
 FROM [Portfolio Project]..NashvilleHousing

 ALTER TABLE [Portfolio Project]..NashvilleHousing
 DROP COLUMN SaleDate
 
 --END
