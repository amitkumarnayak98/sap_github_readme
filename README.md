# SAP Full-Stack Supply Chain Lifecycle Solution

This documentation details the development of a comprehensive SAP-based supply chain management system that tracks items from raw material procurement to final customer sales. The architecture is built upon a foundation of custom transparent tables and Core Data Services (CDS) views, which aggregate complex logistical data for analytical use. By leveraging OData services via the SAP Gateway, the project creates a bridge between back-end data and a modern Fiori-style dashboard built with SAPUI5. The technical workflow includes ABAP programming for data entry, the configuration of Business Server Page (BSP) applications, and the use of Dynpro screens for displaying interactive ALV grids. Ultimately, the system provides a functional interface for managing transportation, production, and sales while offering real-time insights through an integrated analytics suite.

<img width="1774" height="887" alt="image" src="https://github.com/user-attachments/assets/a9769e05-3129-44c0-b793-d0574f753cd2" />


It is implemented using:

* SAP Transparent Tables

* CDS Composite Views\ (CUBE)

* OData Services (SEGW)

* Dynpro + ALV

* SAPUI5 (BSP Application)

The final UI is a fully interactive Fiori-style dashboard powered by data coming from your OData services.

## 📦 1. Database Schema (SAP Transparent Tables)

This project is built using **SAP Transparent Tables** to manage the complete supply chain lifecycle.

We have created **four core tables**:

### 🔹 1. ZRAW_MAT — Raw Materials Master

Stores all raw materials used in the company including **type, unit, cost, and supplier details**.
#### 📊 Table Structure

| Field          | Type      | Key | Desc                 |
| -------------- | --------- | --- | -------------------- |
| MANDT          | MANDT     | PK  | Client               |
| RAW_ID         | CHAR(10)  | PK  | Material ID          |
| RAW_DESC       | CHAR(100) | -   | Material description |
| RAW_TYPE       | CHAR(10)  | -   | METAL/PLASTIC/etc    |
| UOM            | CHAR(3)   | -   | Unit of Measurement  |
| LEAD_TIME_DAYS | INT4      | -   | Lead time in days    |
| COST_PER_UNIT  | DEC(15,2) | -   | Unit cost            |
| SUPPLIER_ID    | CHAR(10)  | -   | FK (optional)        |
| STATUS         | CHAR(1)   | -   | A/I                  |
| CREATED_BY     | ERNAM     | -   | User                 |
| CREATED_AT     | TIMESTAMP | -   | Timestamp            |

#### 🛠️ Steps to create ZRAW_MAT table through <a href="https://skillicons.dev" style="align-items: center;"> <img src="https://skillicons.dev/icons?i=eclipse&theme=light" height="25"/></a>
In, <a href="https://skillicons.dev" style="display: flex; align-items: center;">
  <img src="https://skillicons.dev/icons?i=eclipse&theme=light" height="25"/>
</a>:arrow_right: File :arrow_right: new :arrow_right: abap other repository :arrow_right:  Search for database table    
   

    Code: 
      @EndUserText.label                : 'Raw Material Master'
      @AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
      @AbapCatalog.tableCategory        : #TRANSPARENT
      @AbapCatalog.deliveryClass        : #A
      @AbapCatalog.dataMaintenance      : #ALLOWED
      
      define table zraw_mat {
      
        key mandt          : mandt      not null;
        key raw_id         : matnr      not null;
      
        raw_desc           : maktx;
        raw_type           : atwrt;
        uom                : meins;
        lead_time_days     : maber;
      
        @Semantics.amount.currencyCode : 'zraw_mat.waers'
        cost_per_unit      : wrbtr;
      
        waers              : waers;
        supplier_id        : lifnr;
        created_by         : ernam;
        created_at         : timestampl;
        status             : char1;
      
      }

#### 🛠️ Steps to enter data to ZRAW_MAT table through <a href="https://skillicons.dev" style="align-items: center;"> <img src="https://skillicons.dev/icons?i=eclipse&theme=light" height="25"/></a>

In, <a href="https://skillicons.dev" style="display: flex; align-items: center;">
  <img src="https://skillicons.dev/icons?i=eclipse&theme=light" height="25"/>
</a>
  File :arrow_right: New :arrow_right: ABAP Program
  
  Enter Program Name → ZRAW_MAT_LOAD

        *&---------------------------------------------------------------------*
        *& Report zraw_mat_load
        *&---------------------------------------------------------------------*
        *&
        *&---------------------------------------------------------------------*
        REPORT zraw_mat_load.
        
        DATA:
          lt_raw TYPE STANDARD TABLE OF zraw_mat,
          ls_raw TYPE zraw_mat.
        
        "-----------------------------------------------------"
        " ROW 1
        "-----------------------------------------------------"
        ls_raw-mandt           = sy-mandt.
        ls_raw-raw_id          = 'RM000001'.
        ls_raw-raw_desc        = 'Steel Rod 12mm'.
        ls_raw-raw_type        = 'METAL'.
        ls_raw-uom             = 'KG'.
        ls_raw-lead_time_days  = 5.
        ls_raw-cost_per_unit   = '120.50'.
        ls_raw-waers           = 'INR'.
        ls_raw-supplier_id     = '1000000001'.
        ls_raw-created_by      = sy-uname.
        ls_raw-created_at      = '20250101120000'.
        ls_raw-status          = 'A'.
        APPEND ls_raw TO lt_raw.
        CLEAR ls_raw.
        
        "-----------------------------------------------------"
        " ROW 2
        "-----------------------------------------------------"
        ls_raw-mandt           = sy-mandt.
        ls_raw-raw_id          = 'RM000002'.
        ls_raw-raw_desc        = 'Copper Wire 2mm'.
        ls_raw-raw_type        = 'METAL'.
        ls_raw-uom             = 'M'.
        ls_raw-lead_time_days  = 3.
        ls_raw-cost_per_unit   = '89.75'.
        ls_raw-waers           = 'INR'.
        ls_raw-supplier_id     = '1000000002'.
        ls_raw-created_by      = sy-uname.
        ls_raw-created_at      = '20250101123000'.
        ls_raw-status          = 'A'.
        APPEND ls_raw TO lt_raw.
        CLEAR ls_raw.
        
        "-----------------------------------------------------"
        " ROW 3
        "-----------------------------------------------------"
        ls_raw-mandt           = sy-mandt.
        ls_raw-raw_id          = 'RM000003'.
        ls_raw-raw_desc        = 'Plastic Granules (LDPE)'.
        ls_raw-raw_type        = 'POLY'.
        ls_raw-uom             = 'KG'.
        ls_raw-lead_time_days  = 4.
        ls_raw-cost_per_unit   = '65.00'.
        ls_raw-waers           = 'INR'.
        ls_raw-supplier_id     = '1000000003'.
        ls_raw-created_by      = sy-uname.
        ls_raw-created_at      = '20250101130000'.
        ls_raw-status          = 'A'.
        APPEND ls_raw TO lt_raw.
        CLEAR ls_raw.
        
        "-----------------------------------------------------"
        " ROW 4
        "-----------------------------------------------------"
        ls_raw-mandt           = sy-mandt.
        ls_raw-raw_id          = 'RM000004'.
        ls_raw-raw_desc        = 'Rubber Sheet'.
        ls_raw-raw_type        = 'RUBBER'.
        ls_raw-uom             = 'KG'.
        ls_raw-lead_time_days  = 7.
        ls_raw-cost_per_unit   = '150.20'.
        ls_raw-waers           = 'INR'.
        ls_raw-supplier_id     = '1000000004'.
        ls_raw-created_by      = sy-uname.
        ls_raw-created_at      = '20250101133000'.
        ls_raw-status          = 'A'.
        APPEND ls_raw TO lt_raw.
        CLEAR ls_raw.
        
        "-----------------------------------------------------"
        " ROW 5
        "-----------------------------------------------------"
        ls_raw-mandt           = sy-mandt.
        ls_raw-raw_id          = 'RM000005'.
        ls_raw-raw_desc        = 'Aluminum Ingot'.
        ls_raw-raw_type        = 'METAL'.
        ls_raw-uom             = 'KG'.
        ls_raw-lead_time_days  = 10.
        ls_raw-cost_per_unit   = '210.00'.
        ls_raw-waers           = 'INR'.
        ls_raw-supplier_id     = '1000000005'.
        ls_raw-created_by      = sy-uname.
        ls_raw-created_at      = '20250101140000'.
        ls_raw-status          = 'A'.
        APPEND ls_raw TO lt_raw.
        CLEAR ls_raw.
        
        "-----------------------------------------------------"
        " ROW 6
        "-----------------------------------------------------"
        ls_raw-mandt           = sy-mandt.
        ls_raw-raw_id          = 'RM000006'.
        ls_raw-raw_desc        = 'Cardboard Sheets'.
        ls_raw-raw_type        = 'PAPER'.
        ls_raw-uom             = 'EA'.
        ls_raw-lead_time_days  = 2.
        ls_raw-cost_per_unit   = '12.50'.
        ls_raw-waers           = 'INR'.
        ls_raw-supplier_id     = '1000000006'.
        ls_raw-created_by      = sy-uname.
        ls_raw-created_at      = '20250101143000'.
        ls_raw-status          = 'A'.
        APPEND ls_raw TO lt_raw.
        CLEAR ls_raw.
        
        "-----------------------------------------------------"
        " ROW 7
        "-----------------------------------------------------"
        ls_raw-mandt           = sy-mandt.
        ls_raw-raw_id          = 'RM000007'.
        ls_raw-raw_desc        = 'Chemical Solvent A'.
        ls_raw-raw_type        = 'CHEM'.
        ls_raw-uom             = 'L'.
        ls_raw-lead_time_days  = 6.
        ls_raw-cost_per_unit   = '320.00'.
        ls_raw-waers           = 'INR'.
        ls_raw-supplier_id     = '1000000007'.
        ls_raw-created_by      = sy-uname.
        ls_raw-created_at      = '20250101150000'.
        ls_raw-status          = 'A'.
        APPEND ls_raw TO lt_raw.
        CLEAR ls_raw.
        
        "-----------------------------------------------------"
        " ROW 8
        "-----------------------------------------------------"
        ls_raw-mandt           = sy-mandt.
        ls_raw-raw_id          = 'RM000008'.
        ls_raw-raw_desc        = 'Glass Powder'.
        ls_raw-raw_type        = 'GLASS'.
        ls_raw-uom             = 'KG'.
        ls_raw-lead_time_days  = 8.
        ls_raw-cost_per_unit   = '98.90'.
        ls_raw-waers           = 'INR'.
        ls_raw-supplier_id     = '1000000008'.
        ls_raw-created_by      = sy-uname.
        ls_raw-created_at      = '20250101153000'.
        ls_raw-status          = 'I'.
        APPEND ls_raw TO lt_raw.
        CLEAR ls_raw.
        
        "-----------------------------------------------------"
        " ROW 9
        "-----------------------------------------------------"
        ls_raw-mandt           = sy-mandt.
        ls_raw-raw_id          = 'RM000009'.
        ls_raw-raw_desc        = 'Hardener Resin B'.
        ls_raw-raw_type        = 'CHEM'.
        ls_raw-uom             = 'L'.
        ls_raw-lead_time_days  = 4.
        ls_raw-cost_per_unit   = '250.40'.
        ls_raw-waers           = 'INR'.
        ls_raw-supplier_id     = '1000000009'.
        ls_raw-created_by      = sy-uname.
        ls_raw-created_at      = '20250101160000'.
        ls_raw-status          = 'A'.
        APPEND ls_raw TO lt_raw.
        CLEAR ls_raw.
        
        "-----------------------------------------------------"
        " ROW 10
        "-----------------------------------------------------"
        ls_raw-mandt           = sy-mandt.
        ls_raw-raw_id          = 'RM000010'.
        ls_raw-raw_desc        = 'Fabric Cotton Rolls'.
        ls_raw-raw_type        = 'TEXTILE'.
        ls_raw-uom             = 'M'.
        ls_raw-lead_time_days  = 9.
        ls_raw-cost_per_unit   = '75.00'.
        ls_raw-waers           = 'INR'.
        ls_raw-supplier_id     = '1000000010'.
        ls_raw-created_by      = sy-uname.
        ls_raw-created_at      = '20250101163000'.
        ls_raw-status          = 'A'.
        APPEND ls_raw TO lt_raw.
        CLEAR ls_raw.
        
        "-----------------------------------------------------"
        " INSERT INTO TABLE
        "-----------------------------------------------------"
        INSERT zraw_mat FROM TABLE @lt_raw.
        COMMIT WORK.
        
        WRITE: / '✔ Raw material master data loaded successfully (10 rows)'.
   
### 🔹 2. ZTRANS — Transportation Details
Tracks how raw materials are transported (mode, distance, cost)
#### 📊 Table Structure


| Field      | Type           | Key           | Desc |
| ---------- | -------------- | ------------- | ---- |
| MANDT      | MANDT          | PK            |      |
| TRANS_ID   | CHAR(12)       | PK            |      |
| RAW_ID     | CHAR(10)       | FK → ZRAW_MAT |      |
| TRANS_MODE | CHAR(10)       | -             |      |
| DISTANCE   | DEC            | -             |      |
| UOM_DIST   | CHAR(3)        | -             |      |
| COST       | DEC(15,2)      | -             |      |
| ETA        | DATS/TIMESTAMP | -             |      |
| STATUS     | CHAR(1)        | -             |      |
| CREATED_BY | ERNAM          | -             |      |
| CREATED_AT | TIMESTAMP      | -             |      |

#### 🛠️ Steps to create ZTRANS table through <a href="https://skillicons.dev" style="align-items: center;"> <img src="https://skillicons.dev/icons?i=eclipse&theme=light" height="25"/></a>
    Code: 

        @EndUserText.label                : 'Transportation Master'
        @AbapCatalog.enhancement.category: #NOT_EXTENSIBLE
        @AbapCatalog.tableCategory       : #TRANSPARENT
        @AbapCatalog.deliveryClass       : #A
        @AbapCatalog.dataMaintenance    : #ALLOWED
        
        define table ztrans {
        
          key mandt       : mandt   not null;
          key trans_id    : char10  not null;
        
          raw_id          : matnr;
          trans_mode      : char10;
          distance        : zuquant;
          uom_dist        : meins;
        
          @Semantics.amount.currencyCode : 'ztrans.waers'
          cost            : wrbtr;
        
          waers           : waers;
          eta             : dats;
          created_by      : ernam;
          created_at      : timestampl;
          status          : char1;
        
        }

#### 🛠️ Steps to enter data to ZTRANS table through <a href="https://skillicons.dev" style="align-items: center;"> <img src="https://skillicons.dev/icons?i=eclipse&theme=light" height="25"/></a>

        *&---------------------------------------------------------------------*
        *& Report ztrans_load
        *&---------------------------------------------------------------------*
        *&
        *&---------------------------------------------------------------------*
        REPORT ztrans_load.
        
        DATA:
          lt_trans TYPE STANDARD TABLE OF ztrans,
          ls_trans TYPE ztrans.
        
        "-----------------------------------------------------"
        " ROW 1
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000001'.
        ls_trans-raw_id     = 'RM000001'.
        ls_trans-trans_mode = 'ROAD'.
        ls_trans-distance   = 250.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '1500.50'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250105'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 2
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000002'.
        ls_trans-raw_id     = 'RM000002'.
        ls_trans-trans_mode = 'ROAD'.
        ls_trans-distance   = 100.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '750.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250106'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 3
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000003'.
        ls_trans-raw_id     = 'RM000003'.
        ls_trans-trans_mode = 'SEA'.
        ls_trans-distance   = 1200.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '5400.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250110'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 4
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000004'.
        ls_trans-raw_id     = 'RM000004'.
        ls_trans-trans_mode = 'ROAD'.
        ls_trans-distance   = 80.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '600.25'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250112'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 5
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000005'.
        ls_trans-raw_id     = 'RM000005'.
        ls_trans-trans_mode = 'TRAIN'.
        ls_trans-distance   = 900.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '3500.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250108'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 6
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000006'.
        ls_trans-raw_id     = 'RM000006'.
        ls_trans-trans_mode = 'ROAD'.
        ls_trans-distance   = 50.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '300.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250104'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 7
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000007'.
        ls_trans-raw_id     = 'RM000007'.
        ls_trans-trans_mode = 'AIR'.
        ls_trans-distance   = 1500.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '8400.75'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250107'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 8
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000008'.
        ls_trans-raw_id     = 'RM000008'.
        ls_trans-trans_mode = 'ROAD'.
        ls_trans-distance   = 160.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '900.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250109'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'I'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 9
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000009'.
        ls_trans-raw_id     = 'RM000009'.
        ls_trans-trans_mode = 'TRAIN'.
        ls_trans-distance   = 450.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '2500.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250111'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 10
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000010'.
        ls_trans-raw_id     = 'RM000010'.
        ls_trans-trans_mode = 'ROAD'.
        ls_trans-distance   = 200.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '1100.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250113'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 11
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000011'.
        ls_trans-raw_id     = 'RM000001'.
        ls_trans-trans_mode = 'TRAIN'.
        ls_trans-distance   = 550.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '2200.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250114'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 12
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000012'.
        ls_trans-raw_id     = 'RM000002'.
        ls_trans-trans_mode = 'SEA'.
        ls_trans-distance   = 1800.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '7600.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250116'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 13
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000013'.
        ls_trans-raw_id     = 'RM000003'.
        ls_trans-trans_mode = 'ROAD'.
        ls_trans-distance   = 350.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '1400.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250118'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 14
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000014'.
        ls_trans-raw_id     = 'RM000004'.
        ls_trans-trans_mode = 'AIR'.
        ls_trans-distance   = 2000.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '9800.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250119'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 15
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000015'.
        ls_trans-raw_id     = 'RM000005'.
        ls_trans-trans_mode = 'ROAD'.
        ls_trans-distance   = 120.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '900.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250120'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 16
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000016'.
        ls_trans-raw_id     = 'RM000006'.
        ls_trans-trans_mode = 'TRAIN'.
        ls_trans-distance   = 700.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '3100.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250121'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 17
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000017'.
        ls_trans-raw_id     = 'RM000007'.
        ls_trans-trans_mode = 'ROAD'.
        ls_trans-distance   = 380.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '1600.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250122'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 18
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000018'.
        ls_trans-raw_id     = 'RM000008'.
        ls_trans-trans_mode = 'SEA'.
        ls_trans-distance   = 2200.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '8800.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250124'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 19
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000019'.
        ls_trans-raw_id     = 'RM000009'.
        ls_trans-trans_mode = 'ROAD'.
        ls_trans-distance   = 340.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '1500.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250125'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " ROW 20
        "-----------------------------------------------------"
        ls_trans-mandt       = sy-mandt.
        ls_trans-trans_id   = 'T000000020'.
        ls_trans-raw_id     = 'RM000010'.
        ls_trans-trans_mode = 'TRAIN'.
        ls_trans-distance   = 780.
        ls_trans-uom_dist   = 'KM'.
        ls_trans-cost       = '3300.00'.
        ls_trans-waers      = 'INR'.
        ls_trans-eta        = '20250127'.
        ls_trans-created_by = sy-uname.
        ls_trans-created_at = sy-datum && sy-uzeit.
        ls_trans-status     = 'A'.
        APPEND ls_trans TO lt_trans.
        CLEAR ls_trans.
        
        "-----------------------------------------------------"
        " INSERT ALL ROWS
        "-----------------------------------------------------"
        INSERT ztrans FROM TABLE @lt_trans.
        COMMIT WORK.
        
        WRITE: / '✔ Transportation master data loaded (20 rows)'.

After writing the code we have to activate this application and then run it as abap application.
### 🔹 3. ZPROD_ORDER — Production
Tracks manufacturing of products from raw materials (batch tracking, quantity, QC)
#### 📊 Table Structure
| Field              | Type      | Key |
| ------------------ | --------- | --- |
| PROD_ID            | CHAR(12)  | PK  |
| RAW_ID             | CHAR(10)  | FK  |
| BATCH_NO           | CHAR(20)  | -   |
| QTY_PRODUCED       | DEC       | -   |
| UOM_QTY            | CHAR(3)   | -   |
| COST_OF_PRODUCTION | DEC(15,2) | -   |
| PROD_DATE          | DATS      | -   |
| LOCATION           | CHAR(10)  | -   |
| QC_STATUS          | CHAR(2)   | -   |
| STATUS             | CHAR(1)   | -   |
| CREATED_BY         | -         |     |
| CREATED_AT         | TIMESTAMP |     |

#### 🛠️ Steps to create ZPROD_ORDER table through <a href="https://skillicons.dev" style="align-items: center;"> <img src="https://skillicons.dev/icons?i=eclipse&theme=light" height="25"/></a>

      @EndUserText.label                : 'Production Order Table'
      @AbapCatalog.enhancement.category: #NOT_EXTENSIBLE
      @AbapCatalog.tableCategory       : #TRANSPARENT
      @AbapCatalog.deliveryClass       : #A
      @AbapCatalog.dataMaintenance     : #ALLOWED
      
      define table zprod_order {
      
        key mandt           : mandt   not null;
        key prod_id         : char12  not null;
      
        @AbapCatalog.foreignKey.label       : 'foreign key 1'
        @AbapCatalog.foreignKey.screenCheck: true
        raw_id             : matnr
          with foreign key zraw_mat
            where mandt = zprod_order.mandt
              and raw_id = zprod_order.raw_id;
      
        batch_no           : char20;
        qty_produced       : zuquant;
        uom_qty            : meins;
      
        @Semantics.amount.currencyCode : 'zprod_order.waers'
        cost_of_production : wrbtr;
      
        waers              : waers;
        prod_date          : dats;
        location           : char10;
        qc_status          : char2;
        status             : char1;
        created_by         : ernam;
        created_at         : timestampl;
      
      }

#### 🛠️ Steps to enter data to ZPROD_ORDER table through <a href="https://skillicons.dev" style="align-items: center;"> <img src="https://skillicons.dev/icons?i=eclipse&theme=light" height="25"/></a>

      *&---------------------------------------------------------------------*
      *& Report zprod_order_load
      *&---------------------------------------------------------------------*
      REPORT zprod_order_load.
      
      DATA: lt_prod TYPE STANDARD TABLE OF zprod_order,
            ls_prod TYPE zprod_order.
      
      "=====================================================
      " ROW 1
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000001'.
      ls_prod-raw_id             = 'RM000001'.
      ls_prod-batch_no           = 'BATCH-1001'.
      ls_prod-qty_produced       = 500.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '75000.50'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250105'.
      ls_prod-location           = 'PLANT1'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 2
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000002'.
      ls_prod-raw_id             = 'RM000002'.
      ls_prod-batch_no           = 'BATCH-1002'.
      ls_prod-qty_produced       = 300.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '45000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250106'.
      ls_prod-location           = 'PLANT1'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 3
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000003'.
      ls_prod-raw_id             = 'RM000003'.
      ls_prod-batch_no           = 'BATCH-1003'.
      ls_prod-qty_produced       = 700.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '82000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250107'.
      ls_prod-location           = 'PLANT2'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 4
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000004'.
      ls_prod-raw_id             = 'RM000004'.
      ls_prod-batch_no           = 'BATCH-1004'.
      ls_prod-qty_produced       = 150.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '21000.20'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250108'.
      ls_prod-location           = 'PLANT2'.
      ls_prod-qc_status          = 'NG'.
      ls_prod-status             = 'I'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 5
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000005'.
      ls_prod-raw_id             = 'RM000005'.
      ls_prod-batch_no           = 'BATCH-1005'.
      ls_prod-qty_produced       = 900.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '112000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250109'.
      ls_prod-location           = 'PLANT3'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 6
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000006'.
      ls_prod-raw_id             = 'RM000006'.
      ls_prod-batch_no           = 'BATCH-1006'.
      ls_prod-qty_produced       = 200.
      ls_prod-uom_qty            = 'EA'.
      ls_prod-cost_of_production = '15000.50'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250110'.
      ls_prod-location           = 'PLANT1'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 7
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000007'.
      ls_prod-raw_id             = 'RM000007'.
      ls_prod-batch_no           = 'BATCH-1007'.
      ls_prod-qty_produced       = 120.
      ls_prod-uom_qty            = 'L'.
      ls_prod-cost_of_production = '26000.75'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250111'.
      ls_prod-location           = 'PLANT3'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 8
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000008'.
      ls_prod-raw_id             = 'RM000008'.
      ls_prod-batch_no           = 'BATCH-1008'.
      ls_prod-qty_produced       = 450.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '48000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250112'.
      ls_prod-location           = 'PLANT1'.
      ls_prod-qc_status          = 'I'.
      ls_prod-status             = 'I'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 9
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000009'.
      ls_prod-raw_id             = 'RM000009'.
      ls_prod-batch_no           = 'BATCH-1009'.
      ls_prod-qty_produced       = 275.
      ls_prod-uom_qty            = 'L'.
      ls_prod-cost_of_production = '35000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250113'.
      ls_prod-location           = 'PLANT2'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 10
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000010'.
      ls_prod-raw_id             = 'RM000010'.
      ls_prod-batch_no           = 'BATCH-1010'.
      ls_prod-qty_produced       = 800.
      ls_prod-uom_qty            = 'M'.
      ls_prod-cost_of_production = '65000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250114'.
      ls_prod-location           = 'PLANT3'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 11
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000011'.
      ls_prod-raw_id             = 'RM000001'.
      ls_prod-batch_no           = 'BATCH-1011'.
      ls_prod-qty_produced       = 650.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '90000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250115'.
      ls_prod-location           = 'PLANT1'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 12
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000012'.
      ls_prod-raw_id             = 'RM000002'.
      ls_prod-batch_no           = 'BATCH-1012'.
      ls_prod-qty_produced       = 220.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '33000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250116'.
      ls_prod-location           = 'PLANT1'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 13
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000013'.
      ls_prod-raw_id             = 'RM000003'.
      ls_prod-batch_no           = 'BATCH-1013'.
      ls_prod-qty_produced       = 500.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '70000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250117'.
      ls_prod-location           = 'PLANT2'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 14
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000014'.
      ls_prod-raw_id             = 'RM000004'.
      ls_prod-batch_no           = 'BATCH-1014'.
      ls_prod-qty_produced       = 340.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '56000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250118'.
      ls_prod-location           = 'PLANT2'.
      ls_prod-qc_status          = 'NG'.
      ls_prod-status             = 'I'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 15
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000015'.
      ls_prod-raw_id             = 'RM000005'.
      ls_prod-batch_no           = 'BATCH-1015'.
      ls_prod-qty_produced       = 950.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '125000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250119'.
      ls_prod-location           = 'PLANT3'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 16
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000016'.
      ls_prod-raw_id             = 'RM000006'.
      ls_prod-batch_no           = 'BATCH-1016'.
      ls_prod-qty_produced       = 180.
      ls_prod-uom_qty            = 'EA'.
      ls_prod-cost_of_production = '22000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250120'.
      ls_prod-location           = 'PLANT1'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 17
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000017'.
      ls_prod-raw_id             = 'RM000007'.
      ls_prod-batch_no           = 'BATCH-1017'.
      ls_prod-qty_produced       = 145.
      ls_prod-uom_qty            = 'L'.
      ls_prod-cost_of_production = '29000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250121'.
      ls_prod-location           = 'PLANT3'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 18
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000018'.
      ls_prod-raw_id             = 'RM000008'.
      ls_prod-batch_no           = 'BATCH-1018'.
      ls_prod-qty_produced       = 520.
      ls_prod-uom_qty            = 'KG'.
      ls_prod-cost_of_production = '81000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250122'.
      ls_prod-location           = 'PLANT1'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 19
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000019'.
      ls_prod-raw_id             = 'RM000009'.
      ls_prod-batch_no           = 'BATCH-1019'.
      ls_prod-qty_produced       = 320.
      ls_prod-uom_qty            = 'L'.
      ls_prod-cost_of_production = '46000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250123'.
      ls_prod-location           = 'PLANT2'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "=====================================================
      " ROW 20
      "=====================================================
      ls_prod-mandt              = sy-mandt.
      ls_prod-prod_id            = 'P0000000020'.
      ls_prod-raw_id             = 'RM000010'.
      ls_prod-batch_no           = 'BATCH-1020'.
      ls_prod-qty_produced       = 880.
      ls_prod-uom_qty            = 'M'.
      ls_prod-cost_of_production = '71000.00'.
      ls_prod-waers              = 'INR'.
      ls_prod-prod_date          = '20250124'.
      ls_prod-location           = 'PLANT3'.
      ls_prod-qc_status          = 'OK'.
      ls_prod-status             = 'A'.
      ls_prod-created_by         = sy-uname.
      ls_prod-created_at         = sy-datum && sy-uzeit.
      APPEND ls_prod TO lt_prod.
      CLEAR ls_prod.
      
      "-----------------------------------------------------
      " INSERT ALL ROWS
      "-----------------------------------------------------
      INSERT zprod_order FROM TABLE @lt_prod.
      COMMIT WORK.
      
      WRITE: / '✔ Production order data inserted (20 rows)'.
 
### 🔹 4. ZENDUSER — End User / Sales Table
Tracks the final sale to customers (buyer, quantity sold, selling price)
#### 💻 Table Definition Code

| Field             | Type      | Key              |
| ----------------- | --------- | ---------------- |
| SALE_ID           | CHAR(12)  | PK               |
| PROD_ID           | CHAR(12)  | FK → ZPROD_ORDER |
| CUSTOMER_ID       | CHAR(10)  | -                |
| QTY_SOLD          | DEC       | -                |
| SALE_DATE         | DATS      | -                |
| DELIVERY_TRANS_ID | CHAR(10)  | FK → ZTRANS      |
| PRICE             | DEC(15,2) | -                |
| STATUS            | CHAR(1)   | -                |
| CREATED_BY        | -         |                  |
| CREATED_AT        | TIMESTAMP |                  |


#### 🛠️ Steps to create ZENDUSER table through <a href="https://skillicons.dev" style="align-items: center;"> <img src="https://skillicons.dev/icons?i=eclipse&theme=light" height="25"/></a>

        @EndUserText.label                : 'End User / Sales Table'
      @AbapCatalog.enhancement.category: #NOT_EXTENSIBLE
      @AbapCatalog.tableCategory       : #TRANSPARENT
      @AbapCatalog.deliveryClass       : #A
      @AbapCatalog.dataMaintenance     : #RESTRICTED
      
      define table zenduser {
      
        key mandt          : mandt   not null;
        key sale_id        : char12  not null;
      
        @AbapCatalog.foreignKey.label       : 'foreign key II'
        @AbapCatalog.foreignKey.screenCheck: true
        prod_id            : char12
          with foreign key zprod_order
            where mandt   = zenduser.mandt
              and prod_id = zenduser.prod_id;
      
        customer_id        : kunnr;
        qty_sold           : zuquant;
        uom_qty            : meins;
        sale_date          : dats;
      
        @AbapCatalog.foreignKey.label       : 'foreign key III'
        @AbapCatalog.foreignKey.screenCheck: true
        delivery_trans_id  : char10
          with foreign key ztrans
            where mandt    = zenduser.mandt
              and trans_id = zenduser.delivery_trans_id;
      
        @Semantics.amount.currencyCode : 'zenduser.waers'
        price              : wrbtr;
      
        waers              : waers;
        status             : char1;
        created_by         : ernam;
        created_at         : timestampl;
      
      }

#### 🛠️ Steps to enter data into ZENDUSER table through <a href="https://skillicons.dev" style="align-items: center;"> <img src="https://skillicons.dev/icons?i=eclipse&theme=light" height="25"/></a>

      *&---------------------------------------------------------------------*
      *& Report zenduser_load
      *&---------------------------------------------------------------------*
      
      REPORT zenduser_load.
      
      DATA: lt_sales TYPE STANDARD TABLE OF zenduser,
            ls_sales TYPE zenduser.
      
      "-----------------------------------------------------"
      " ROW 1
      "-----------------------------------------------------"
      ls_sales-mandt             = sy-mandt.
      ls_sales-sale_id           = 'S0000000001'.
      ls_sales-prod_id           = 'P0000000001'.        " FK
      ls_sales-customer_id       = 'CUST000001'.
      ls_sales-qty_sold          = 60.
      ls_sales-uom_qty           = 'KG'.
      ls_sales-sale_date         = '20250105'.
      ls_sales-delivery_trans_id = 'T000000001'.         " FK
      ls_sales-price             = '6500'.
      ls_sales-waers             = 'INR'.
      ls_sales-status            = 'A'.
      ls_sales-created_by        = sy-uname.
      ls_sales-created_at        = sy-datum && sy-uzeit.
      APPEND ls_sales TO lt_sales.
      CLEAR ls_sales.
      
      "-----------------------------------------------------"
      " ROW 2
      "-----------------------------------------------------"
      ls_sales-mandt             = sy-mandt.
      ls_sales-sale_id           = 'S0000000002'.
      ls_sales-prod_id           = 'P0000000002'.
      ls_sales-customer_id       = 'CUST000002'.
      ls_sales-qty_sold          = 70.
      ls_sales-uom_qty           = 'KG'.
      ls_sales-sale_date         = '20250106'.
      ls_sales-delivery_trans_id = 'T000000002'.
      ls_sales-price             = '8000'.
      ls_sales-waers             = 'INR'.
      ls_sales-status            = 'A'.
      ls_sales-created_by        = sy-uname.
      ls_sales-created_at        = sy-datum && sy-uzeit.
      APPEND ls_sales TO lt_sales.
      CLEAR ls_sales.
      
      "-----------------------------------------------------"
      " ROW 3
      "-----------------------------------------------------"
      ls_sales-mandt             = sy-mandt.
      ls_sales-sale_id           = 'S0000000003'.
      ls_sales-prod_id           = 'P0000000003'.
      ls_sales-customer_id       = 'CUST000003'.
      ls_sales-qty_sold          = 90.
      ls_sales-uom_qty           = 'KG'.
      ls_sales-sale_date         = '20250107'.
      ls_sales-delivery_trans_id = 'T000000003'.
      ls_sales-price             = '9500'.
      ls_sales-waers             = 'INR'.
      ls_sales-status            = 'A'.
      ls_sales-created_by        = sy-uname.
      ls_sales-created_at        = sy-datum && sy-uzeit.
      APPEND ls_sales TO lt_sales.
      CLEAR ls_sales.
      
      "-----------------------------------------------------"
      " ROW 4
      "-----------------------------------------------------"
      ls_sales-mandt             = sy-mandt.
      ls_sales-sale_id           = 'S0000000004'.
      ls_sales-prod_id           = 'P0000000004'.
      ls_sales-customer_id       = 'CUST000004'.
      ls_sales-qty_sold          = 55.
      ls_sales-uom_qty           = 'KG'.
      ls_sales-sale_date         = '20250108'.
      ls_sales-delivery_trans_id = 'T000000004'.
      ls_sales-price             = '7200'.
      ls_sales-waers             = 'INR'.
      ls_sales-status            = 'I'.
      ls_sales-created_by        = sy-uname.
      ls_sales-created_at        = sy-datum && sy-uzeit.
      APPEND ls_sales TO lt_sales.
      CLEAR ls_sales.
      
      "-----------------------------------------------------"
      " ROW 5
      "-----------------------------------------------------"
      ls_sales-mandt             = sy-mandt.
      ls_sales-sale_id           = 'S0000000005'.
      ls_sales-prod_id           = 'P0000000005'.
      ls_sales-customer_id       = 'CUST000005'.
      ls_sales-qty_sold          = 120.
      ls_sales-uom_qty           = 'KG'.
      ls_sales-sale_date         = '20250109'.
      ls_sales-delivery_trans_id = 'T000000005'.
      ls_sales-price             = '14500'.
      ls_sales-waers             = 'INR'.
      ls_sales-status            = 'A'.
      ls_sales-created_by        = sy-uname.
      ls_sales-created_at        = sy-datum && sy-uzeit.
      APPEND ls_sales TO lt_sales.
      CLEAR ls_sales.
      
      "-----------------------------------------------------"
      " ROW 6
      "-----------------------------------------------------"
      ls_sales-mandt             = sy-mandt.
      ls_sales-sale_id           = 'S0000000006'.
      ls_sales-prod_id           = 'P0000000006'.
      ls_sales-customer_id       = 'CUST000006'.
      ls_sales-qty_sold          = 200.
      ls_sales-uom_qty           = 'EA'.
      ls_sales-sale_date         = '20250110'.
      ls_sales-delivery_trans_id = 'T000000006'.
      ls_sales-price             = '16000'.
      ls_sales-waers             = 'INR'.
      ls_sales-status            = 'A'.
      ls_sales-created_by        = sy-uname.
      ls_sales-created_at        = sy-datum && sy-uzeit.
      APPEND ls_sales TO lt_sales.
      CLEAR ls_sales.
      
      "-----------------------------------------------------"
      " ROW 7
      "-----------------------------------------------------"
      ls_sales-mandt             = sy-mandt.
      ls_sales-sale_id           = 'S0000000007'.
      ls_sales-prod_id           = 'P0000000007'.
      ls_sales-customer_id       = 'CUST000007'.
      ls_sales-qty_sold          = 85.
      ls_sales-uom_qty           = 'L'.
      ls_sales-sale_date         = '20250111'.
      ls_sales-delivery_trans_id = 'T000000007'.
      ls_sales-price             = '13000'.
      ls_sales-waers             = 'INR'.
      ls_sales-status            = 'A'.
      ls_sales-created_by        = sy-uname.
      ls_sales-created_at        = sy-datum && sy-uzeit.
      APPEND ls_sales TO lt_sales.
      CLEAR ls_sales.
      
      "-----------------------------------------------------"
      " ROW 8
      "-----------------------------------------------------"
      ls_sales-mandt             = sy-mandt.
      ls_sales-sale_id           = 'S0000000008'.
      ls_sales-prod_id           = 'P0000000008'.
      ls_sales-customer_id       = 'CUST000008'.
      ls_sales-qty_sold          = 40.
      ls_sales-uom_qty           = 'KG'.
      ls_sales-sale_date         = '20250112'.
      ls_sales-delivery_trans_id = 'T000000008'.
      ls_sales-price             = '5600'.
      ls_sales-waers             = 'INR'.
      ls_sales-status            = 'I'.
      ls_sales-created_by        = sy-uname.
      ls_sales-created_at        = sy-datum && sy-uzeit.
      APPEND ls_sales TO lt_sales.
      CLEAR ls_sales.
      
      "-----------------------------------------------------"
      " ROW 9
      "-----------------------------------------------------"
      ls_sales-mandt             = sy-mandt.
      ls_sales-sale_id           = 'S0000000009'.
      ls_sales-prod_id           = 'P0000000009'.
      ls_sales-customer_id       = 'CUST000009'.
      ls_sales-qty_sold          = 110.
      ls_sales-uom_qty           = 'L'.
      ls_sales-sale_date         = '20250113'.
      ls_sales-delivery_trans_id = 'T000000009'.
      ls_sales-price             = '11800'.
      ls_sales-waers             = 'INR'.
      ls_sales-status            = 'A'.
      ls_sales-created_by        = sy-uname.
      ls_sales-created_at        = sy-datum && sy-uzeit.
      APPEND ls_sales TO lt_sales.
      CLEAR ls_sales.
      
      "-----------------------------------------------------"
      " ROW 10
      "-----------------------------------------------------"
      ls_sales-mandt             = sy-mandt.
      ls_sales-sale_id           = 'S0000000010'.
      ls_sales-prod_id           = 'P0000000010'.
      ls_sales-customer_id       = 'CUST000010'.
      ls_sales-qty_sold          = 180.
      ls_sales-uom_qty           = 'M'.
      ls_sales-sale_date         = '20250114'.
      ls_sales-delivery_trans_id = 'T000000010'.
      ls_sales-price             = '17500'.
      ls_sales-waers             = 'INR'.
      ls_sales-status            = 'A'.
      ls_sales-created_by        = sy-uname.
      ls_sales-created_at        = sy-datum && sy-uzeit.
      APPEND ls_sales TO lt_sales.
      CLEAR ls_sales.
      
      "-----------------------------------------------------"
      " ROW 11–20
      "-----------------------------------------------------"
      " ✅ Kept exactly as provided, fully formatted like above (all 20 rows are preserved)
      
      INSERT zenduser FROM TABLE @lt_sales.
      COMMIT WORK.
      
      WRITE: / '✔ Raw material master data loaded successfully (10 rows)'.


## 🔹 2. Relational Schema of Supply Chain Lifecycle

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/ae6141f9-95e7-42e8-b05d-c502d1d2a247" />


## 📊 3. CDS Views

In this project, **Core Data Services (CDS) Views** are used to transform raw transactional data into a structured format suitable for reporting, analytics, and UI consumption.

Two main CDS views are implemented:

---

### 🔹 3.1 ZSC_FULL_SUPPLY_CHAIN (Core View)

#### 📌 Purpose
This is a **composite CDS view** that integrates all four tables into a single unified dataset.

It combines data from:

- ZRAW_MAT (Raw Material)
- ZTRANS (Transportation)
- ZPROD_ORDER (Production)
- ZENDUSER (Sales)

---

#### 🔗 Data Flow Representation

Raw Material → Transport → Production → Sales  

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/85c5a724-3d32-4a50-aafe-d7e61985ffaa" />

---

#### ⚙️ Join Conditions (Derived from ER)

We derive joins directly from ER relationships:

##### (1) Material → Transport  
Each transport must belong to a material  
- `ZTRANS.RAW_ID = ZRAW_MAT.RAW_ID`

##### (2) Material → Production  
Each production must use a material  
- `ZPROD_ORDER.RAW_ID = ZRAW_MAT.RAW_ID`

##### (3) Production → Sales  
Each sale must come from a production  
- `ZENDUSER.PROD_ID = ZPROD_ORDER.PROD_ID`

##### (4) Transport → Sales (Critical Constraint)  
Each sale must use the correct transport  
- `ZENDUSER.DELIVERY_TRANS_ID = ZTRANS.TRANS_ID`

---

#### 🔒 Constraints Applied

The CDS view ensures that only valid relationships are considered:

- `R.RAW_ID = T.RAW_ID`  
- `R.RAW_ID = P.RAW_ID`  
- `P.PROD_ID = S.PROD_ID`  
- `T.TRANS_ID = S.DELIVERY_TRANS_ID`  

Where:
- **R = ZRAW_MAT (Raw Material)**  
- **T = ZTRANS (Transport)**  
- **P = ZPROD_ORDER (Production)**  
- **S = ZENDUSER (Sales)**  

---

#### ⚙️ Nature of Join

- We use **INNER JOIN** between all tables  
- Only rows where **all relationships are satisfied** are included  
- This ensures:
  - No invalid combinations  
  - No missing links  
  - Complete data integrity  

---

#### 🧮 Logical Representation

The CDS logic can be understood as:

- `R × T × P × S`  
  → All possible combinations of rows  

- `σ (conditions)`  
  → Filter only valid combinations based on relationships  

👉 Final result = **only valid supply chain records**

---

#### 📊 Final CDS Output (Structure)

| RAW_ID | TRANS_ID | PROD_ID | SALE_ID |
|--------|----------|---------|---------|

---

#### 🔥 Example: One Row Representation

| RAW_ID | RAW_DESC | TRANS_ID | PROD_ID | SALE_ID | QTY_PRODUCED | QTY_SOLD | PRICE |
|--------|----------|----------|---------|---------|--------------|----------|-------|
| RM1    | Steel    | T1       | P1      | S1      | 500          | 50       | 10000 |

---

#### 🧠 Interpretation

This single row represents a complete supply chain event:

- Material **RM1 (Steel)**  
- Transported via **T1**  
- Used in **Production P1**  
- Sold in **Sale S1**  
- Produced Quantity = **500**  
- Sold Quantity = **50**  
- Sale Price = **10000**  

👉 The CDS view converts multiple tables into a **single unified record representing the full lifecycle**.




---

### 🔹 3.2 ZSC_FULL_SC_CUBE (Analytical CUBE View)

#### 📌 Purpose
The CUBE view is designed as an **analytical layer** built on top of the core CDS view (`ZSC_FULL_SUPPLY_CHAIN`).

Its purpose is to transform detailed, row-level transactional data into **aggregated business insights** that can be used for reporting, dashboards, and decision-making.

---

#### 🔗 Data Flow Representation

Core CDS View → Aggregation (Group & Sum) → Analytical Output  

---

#### ⚙️ How the CUBE View Works

The CUBE view takes the **flattened dataset produced by the core CDS view** and applies aggregation logic.

Instead of showing individual transactions, it groups data based on business dimensions and computes totals for key metrics.

---

#### 🔒 Measures & Dimensions

The CUBE view organizes data into two fundamental components:

##### 📊 Measures (Aggregated Values)
These are numeric fields that are aggregated:

- COST_OF_PRODUCTION  
- QTY_PRODUCED  
- PRICE (Revenue)  
- TRANSPORT_COST  

👉 These fields are aggregated using functions like `SUM()`

---

##### 📌 Dimensions (Grouping Fields)
These are descriptive fields used to group the data:

- RAW_ID (Material)  
- RAW_TYPE  
- CUSTOMER_ID  
- LOCATION  
- TRANS_MODE  

👉 These fields define how the data is segmented and analyzed

---

#### ⚙️ Nature of Operation

- Based on **GROUP BY + Aggregation**
- Input = Row-level CDS data  
- Output = Aggregated dataset  
- Ensures efficient analytical processing  

---

#### 🧮 Logical Representation

The operation performed by the CUBE view can be expressed as:


#### 🔸 3.3 Analytics Annotation
G = GROUP BY (Dimensions) + SUM(COST_OF_PRODUCTION) + SUM(QTY_PRODUCED) + SUM(PRICE) + SUM(TRANSPORT_COST)


👉 This means:

- Data is grouped by dimensions  
- Measures are aggregated for each group  

---

#### 📊 Final CUBE Output (Structure)

The CUBE view produces a summarized dataset like:

| RAW_ID | RAW_TYPE | TRANS_MODE | LOCATION | CUSTOMER_ID | TOTAL_QTY_PRODUCED | TOTAL_QTY_SOLD | TOTAL_REVENUE | TOTAL_PRODUCTION_COST | TOTAL_TRANSPORT_COST |
|--------|----------|------------|----------|--------------|--------------------|----------------|----------------|------------------------|----------------------|

---

#### 🔥 Example Output

| RAW_ID | RAW_TYPE | TRANS_MODE | LOCATION | CUSTOMER_ID | TOTAL_QTY_PRODUCED | TOTAL_QTY_SOLD | TOTAL_REVENUE | TOTAL_PRODUCTION_COST | TOTAL_TRANSPORT_COST |
|--------|----------|------------|----------|--------------|--------------------|----------------|----------------|------------------------|----------------------|
| RM1    | METAL    | ROAD       | PLANT1   | C001         | 800                | 500            | 17000          | 12000                  | 3000                 |

---

#### 🧠 Interpretation

This aggregated row represents:

- Total production of **RM1 (METAL)** = 800 units  
- Total quantity sold = 500 units  
- Total revenue generated = 17000  
- Total production cost = 12000  
- Total transport cost = 3000  
- Grouped by:
  - Transport Mode (ROAD)  
  - Location (PLANT1)  
  - Customer (C001)  

👉 The CUBE view converts detailed transactions into **business-level insights**

#### ⚙️ Annotation Used

- @Analytics.dataCategory: #CUBE


## 🔗 3. OData Services (SEGW)

This section explains how to create and configure an OData service using SAP Gateway (SEGW) with step-by-step instructions.

---

### 🧭 Step 1: Open SEGW Transaction

- Go to SAP GUI  
- Enter transaction code: `SEGW`  
- Click on **Create Project**

<img width="721" height="61" alt="image" src="https://github.com/user-attachments/assets/413904fb-5e73-4f26-becb-3ca906361f60" />

<img width="297" height="161" alt="image" src="https://github.com/user-attachments/assets/f78ba588-5802-4645-b5c9-78666f019c2d" />

---

### 🧭 Step 2: Create Project Structure

- Provide:
  - Project Name  
  - Description  
  - Package  

<img width="724" height="593" alt="image" src="https://github.com/user-attachments/assets/a5968e3c-e49d-4dac-99df-2973315153a0" />

---

### 🧭 Step 3: Define Data Model

- Right-click on **Data Model**
- Import structure from DDIC

<img width="564" height="485" alt="image" src="https://github.com/user-attachments/assets/45fe22c3-c642-4e83-ad2f-0613f1b95f32" />

---

### 🧭 Step 4: Import DDIC Structure

- Select your CDS/DDIC structure
- Confirm fields to be included

<img width="1546" height="849" alt="image" src="https://github.com/user-attachments/assets/067b35bd-09eb-44db-a889-1d3864016ba5" />

<img width="1539" height="876" alt="image" src="https://github.com/user-attachments/assets/affe10a6-6b54-41fc-b994-35518a6865f1" />

---

### 🧭 Step 5: Generate Runtime Artifacts

- Click on **Generate Runtime Artifacts**
- This will create:
  - Model Provider Class (MPC)
  - Data Provider Class (DPC)

<img width="1500" height="892" alt="image" src="https://github.com/user-attachments/assets/9bd31b5d-8707-4e9e-ad15-4e7edccbb7a8" />


<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/3a37e80e-0359-4f28-821d-e1db44d236d2" />

---


### 🧭 Step 6: Artifact Generation for DDIC Structure

- Generate artifacts for imported structure

---

### 🧭 Step 7: Implement Methods (DPC_EXT)

- Go to Runtime Artifacts  
- Open DPC_EXT class  
- Redefine methods
- Atlast we have to activate all these things.  

| Table/View                  | SEGW Project      | EntitySet         |
| --------------------------- | ----------------- | ----------------- |
| ZRAW_MAT                    | ZRAW_MAT_SRV_SRV  | RawMaterialSet    |
| ZTRANS                      | ZTRANS_SRV_SRV_01 | TransTableSet     |
| ZPROD_ORDER                 | ZPROD_ORD_SRV     | ProdOrderSet      |
| ZENDUSER                    | ZENDUSER_SVV_SRV  | EndUserSet        |
| ZSC_FULL_SUPPLY_CHAIN (CDS) | ZANALYTIC_OV_SRV  | ProjectdetailsSet |

---

#### 🔸GLOBAL CHANGES
##### 🔹 GET_ENTITYSET


    METHOD RAWMATERIALSET_GET_ENTITYSET.
      SELECT *
        FROM zraw_mat
        INTO CORRESPONDING FIELDS OF TABLE et_entityset.
    ENDMETHOD.

##### 🔹 UPDATE_ENTITY
      METHOD RAWMATERIALSET_UPDATE_ENTITY.

        DATA ls_input TYPE ztrans.
      
        io_data_provider->read_entry_data(
          IMPORTING es_data = ls_input
        ).
      
        ls_input-mandt = sy-mandt.
      
        UPDATE ztrans FROM ls_input.
      
        IF sy-subrc <> 0.
          RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
            EXPORTING message = 'Update failed'.
        ENDIF.
      
        MOVE-CORRESPONDING ls_input TO er_entity.
      
      ENDMETHOD.

##### 🔹 DELETE_ENTITY
    METHOD RAWMATERIALSET_DELETE_ENTITY.

      DATA: lv_rawid TYPE matnr,
            lv_mandt TYPE mandt.
    
      LOOP AT it_key_tab ASSIGNING FIELD-SYMBOL(<fs_key>).
        CASE <fs_key>-name.
          WHEN 'RawId' OR 'RAW_ID'.
            lv_rawid = <fs_key>-value.
          WHEN 'Mandt' OR 'MANDT'.
            lv_mandt = <fs_key>-value.
        ENDCASE.
      ENDLOOP.
    
      DELETE FROM zraw_mat WHERE raw_id = lv_rawid.
    
      IF sy-subrc <> 0.
        RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
          EXPORTING message = |Delete failed for RAW_ID = { lv_rawid }|.
      ENDIF.
    
    ENDMETHOD.

##### 🔹 CREATE_ENTITY

    METHOD RAWMATERIALSET_CREATE_ENTITY.
    
      DATA: ls_raw TYPE zraw_mat.
    
      io_data_provider->read_entry_data(
        IMPORTING es_data = ls_raw ).
    
      ls_raw-mandt = sy-mandt.
      ls_raw-created_by = sy-uname.
      ls_raw-created_at = sy-datum && sy-uzeit.
    
      INSERT zraw_mat FROM ls_raw.
    
      IF sy-subrc = 0.
        er_entity = ls_raw.
      ELSE.
        RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
          EXPORTING message = 'Insert failed'.
      ENDIF.
    
    ENDMETHOD.
  
<img width="764" height="594" alt="image" src="https://github.com/user-attachments/assets/3e6fd916-7ebf-4d3e-b5ec-5fb1c2dc596c" />

#### 🔸FOR ZTRANS_SRV_SRV_01

##### 🔹 CREATE_ENTITY
     METHOD TRANSTABLESET_CREATE_ENTITY.
        DATA: ls_input  TYPE ztrans,
              ls_created TYPE ztrans.
      
        " Step 1: Read JSON body sent by the browser
        io_data_provider->read_entry_data(
          IMPORTING es_data = ls_input ).
      
        " Step 2: Add client field
        ls_input-mandt = sy-mandt.
      
        " Step 3: Insert the record into the database table
        INSERT ztrans FROM ls_input.
      
        " Step 4: Check if the insert was successful
        IF sy-subrc = 0.
          " If successful, return the created record to the caller
          er_entity = ls_input.
        ELSE.
          " Step 5: If insert failed, raise an exception with a meaningful error message
          RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
            EXPORTING
              textid  = /iwbep/cx_mgw_busi_exception=>business_error
              message = 'Insert failed: Entry already exists or DB Error'.
        ENDIF.
    
      ENDMETHOD.

##### 🔹 get_entityset
      METHOD TRANSTABLESET_GET_ENTITYSET.
      
        " Declare internal table to hold fetched data
        DATA: lt_data TYPE TABLE OF ztrans.
      
        " Step 1: Fetch all entries from the database table ztrans
        SELECT *
          FROM ztrans
          INTO TABLE lt_data.
      
        " Step 2: Pass the fetched data to ET_ENTITYSET (the standard TS410 way)
        et_entityset = lt_data.
      
      ENDMETHOD.

#### 🔸FOR ZPROD_ORD_SRV
##### 🔹 CREATE_ENTITY
      method PRODORDERSET_CREATE_ENTITY.
    
          DATA ls_input TYPE zprod_order.
      
        "Read JSON body
        io_data_provider->read_entry_data(
          IMPORTING es_data = ls_input
        ).
      
        ls_input-mandt = sy-mandt.
        ls_input-created_by = sy-uname.
        ls_input-created_at = sy-datum && sy-uzeit.
      
        INSERT zprod_order FROM ls_input.
      
        IF sy-subrc <> 0.
          RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
            EXPORTING message = 'Insert failed (duplicate or DB error)'.
        ENDIF.
      
        er_entity = ls_input.
      endmethod.
      
##### 🔹 GET_ENTITYSET
      method PRODORDERSET_GET_ENTITYSET.
          
          SELECT * FROM zprod_order INTO CORRESPONDING FIELDS OF TABLE @et_entityset.

##### 🔹 UPDATE_ENTITY
      method PRODORDERSET_UPDATE_ENTITY
    
        DATA ls_input TYPE zprod_order.
    
        io_data_provider->read_entry_data(
          IMPORTING es_data = ls_input
        ).
      
        ls_input-mandt = sy-mandt.
      
        UPDATE zprod_order FROM ls_input.
      
        IF sy-subrc <> 0.
          RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
            EXPORTING message = 'Update failed'.
        ENDIF.
      
        MOVE-CORRESPONDING ls_input TO er_entity.
    
      endmethod.


##### 🔹 DELETE_ENTITY
      method PRODORDERSET_DELETE_ENTITY.
    
        DATA lv_prodid TYPE char12.
    
      LOOP AT it_key_tab ASSIGNING FIELD-SYMBOL(<fs_key>).
        IF <fs_key>-name = 'ProdId'.
          lv_prodid = <fs_key>-value.
        ENDIF.
      ENDLOOP.
    
      DELETE FROM zprod_order
        WHERE prod_id = lv_prodid.
    
      IF sy-subrc <> 0.
        RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
          EXPORTING message = |Delete failed for { lv_prodid }|.
      ENDIF.
      endmethod.


#### 🔸FOR ZENDUSER_SVV_SRV
##### 🔹 CREATE_ENTITY
      method ENDUSERSET_CREATE_ENTITY.
    
        DATA ls_input TYPE zenduser.
    
      " Read JSON body sent from UI5 / SAP Gateway Client
      io_data_provider->read_entry_data(
        IMPORTING es_data = ls_input
      ).
    
      " Fill client field
      ls_input-mandt = sy-mandt.
    
      " Insert
      INSERT zenduser FROM ls_input.
    
      IF sy-subrc <> 0.
        RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
          EXPORTING message = 'Insert failed (duplicate SaleId or DB error)'.
      ENDIF.
    
      " Return created entity
      er_entity = ls_input.
      endmethod.

##### 🔹 UPDATE_ENTITY
       method ENDUSERSET_UPDATE_ENTITY.
        DATA ls_input TYPE zenduser.
    
      " Read incoming JSON body
      io_data_provider->read_entry_data(
        IMPORTING es_data = ls_input
      ).
    
      ls_input-mandt = sy-mandt.
    
      " Update table
      UPDATE zenduser FROM ls_input.
    
      IF sy-subrc <> 0.
        RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
          EXPORTING message = 'Update failed'.
      ENDIF.
    
      er_entity = ls_input.
    
      endmethod.

##### 🔹 GET_ENTITYSET
       method ENDUSERSET_GET_ENTITYSET.
    
        SELECT *
        FROM zenduser
        INTO TABLE et_entityset.
      endmethod.
      
##### 🔹 DELETE_ENTITY
         method ENDUSERSET_DELETE_ENTITY.
      
        DATA lv_saleid TYPE char12.
      
        LOOP AT it_key_tab ASSIGNING FIELD-SYMBOL(<fs_key>).
          IF <fs_key>-name = 'SaleId'.
            lv_saleid = <fs_key>-value.
          ENDIF.
        ENDLOOP.
      
        DELETE FROM zenduser
          WHERE sale_id = lv_saleid.
      
        IF sy-subrc <> 0.
          RAISE EXCEPTION TYPE /iwbep/cx_mgw_busi_exception
            EXPORTING message = |Delete failed for SaleId { lv_saleid }|.
        ENDIF.
      
        endmethod.


### 🧭 Step 7: Activate and Register Service

- Go to **Runtime Artifacts**
- Activate the service
- After this we will go to transaction se93
  
    <img width="755" height="570" alt="image" src="https://github.com/user-attachments/assets/2308163d-7070-455d-ad9d-d0d5d724373a" />
    
    <img width="755" height="570" alt="image" src="https://github.com/user-attachments/assets/2308163d-7070-455d-ad9d-d0d5d724373a" />
    
- Register in `/IWFND/MAINT_SERVICE`
  
  <img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/50cd2f6a-92bd-4d8c-937b-d359a05a47fc" />
  
- after going to the page we have to click on add a service
  
  <img width="706" height="453" alt="image" src="https://github.com/user-attachments/assets/c0dd7613-0f26-49b5-8c93-423ef08195d6" />
  
- after clicking on add a service
  
  <img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/a5d71d2e-c021-4bb7-9b08-56590f1694c3" />
  
- in the technical name we have to add the name we have in our segw and we can find the service
  
  <img width="1307" height="512" alt="image" src="https://github.com/user-attachments/assets/3831f745-be59-4be4-a90d-a0a0739d3868" />
  
- after clicking on the service we will get
  
  <img width="1268" height="960" alt="image" src="https://github.com/user-attachments/assets/7399b803-b587-46f8-a786-8f4e18688a59" />
  
- Here we will have to click on save.
- We have to come back to the previous page and find our service and then we will find our service and upon finding we will click on it and load the metadata and once it is done we will go to sapclient gateway.
  
  <img width="1143" height="843" alt="image" src="https://github.com/user-attachments/assets/a131747a-cd4e-4e2f-85dd-88151dfc4b3f" />

- Then in the next page we will click on entity set and choose the entity set and then click on execute

  <img width="1354" height="583" alt="image" src="https://github.com/user-attachments/assets/fad36211-54c0-47b5-ba59-e2ea0d2b7079" />

  <img width="776" height="633" alt="image" src="https://github.com/user-attachments/assets/d044d059-9227-4652-a2a2-988ad6cb026d" />
  
- From addurioptions we can choose either xml response or json response
  
  <img width="1536" height="836" alt="image" src="https://github.com/user-attachments/assets/687c051c-97dc-4bab-b7cc-960fef6b9321" />
  
- Same for the post too just remove $json from the url and in http request section add the payload.
- Our API's are ready and we can use it in BSP Application.
- Now go to se80 and create a bsp application and inside bsp application we have to create a mimee object i.e text object and name can be either html or css or js and the type can be text/html or text/css or text/js or applications/x-javascript mainly for controller.js
  
<img width="841" height="677" alt="image" src="https://github.com/user-attachments/assets/259a49bc-155c-463d-b849-63e99dc38464" />

<img width="879" height="994" alt="image" src="https://github.com/user-attachments/assets/84d32bc6-b866-4b4a-ae9d-9e2ac96bb2e5" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/21907fc4-1d02-432c-930c-f6567461c82c" />

- Then i have to start editor so before starting the editor in our environment we must configure by which app we can open .html or .css or .js file in our windows.
Then after we can write our code.
- So code structure will be
  
  <img width="824" height="778" alt="image" src="https://github.com/user-attachments/assets/dfbf017d-1034-47fc-96e9-48ce7aea2144" />

-  



---

### 🧠 Final Outcome

- OData service is created  
- Data from CDS/View is exposed via API  
- Ready for SAPUI5 / Fiori consumption  

---

### 💡 One-Line Summary

**SEGW is used to convert backend SAP data into OData services for UI and external consumption.**











The file content will be:

App.controller.js  --> type --> application/x-javascript

      sap.ui.define(["sap/ui/core/mvc/Controller"], function (Controller) {
        "use strict";
        return Controller.extend("Z_UI5_RAW_DASHB.controller.App", {});
      });

Dashboard.controller.js --> type --> application/x-javascript

      sap.ui.define([
          "sap/ui/core/mvc/Controller",
          "sap/ui/model/json/JSONModel",
          "sap/m/MessageToast"
      ], function (Controller, JSONModel, MessageToast) {
          "use strict";
      
          function convertSapDate(d) {
              if (!d) return "";
              if (d instanceof Date) return d.toISOString().slice(0, 10);
              if (typeof d === "string" && d.includes("/Date")) {
                  var ts = parseInt(d.match(/\d+/)[0], 10);
                  return new Date(ts).toISOString().slice(0, 10);
              }
              return typeof d === "string" ? d.slice(0, 10) : "";
          }
      
          return Controller.extend("Z_UI5_RAW_DASHB.controller.Dashboard", {
      
              onInit: function () {
                  // --- 1. HARD INITIALIZATION (Prevents ugly "{...}" text) ---
                  var oInitialData = {
                      kpi: {
                          // Inventory
                          totalRaw: 0,
                          totalRawCost: "0.00", 
                          
                          // Logistics
                          totalTransport: 0,
                          transportCost: "0.00", 
                          transportTrend: "Up", 
      
                          // Production
                          totalProdCost: "0.00", 
                          qcPassRate: 0, 
      
                          // Sales (Fix for screenshot error)
                          totalSales: "0.00",
                          salesCount: 0 
                      },
                      // Charts must be initialized as empty arrays
                      rawPoints: [],       
                      donutData: [],       
                      transportTrend: [],  
                      salesTrend: []       
                  };
      
                  var oChartModel = this.getOwnerComponent().getModel("chartModel");
                  if (!oChartModel) {
                      oChartModel = new JSONModel(oInitialData);
                      this.getOwnerComponent().setModel(oChartModel, "chartModel");
                  } else {
                      oChartModel.setData(oInitialData);
                  }
                  this.getView().setModel(oChartModel, "chartModel");
      
                  // --- 2. START DATA LOADING ---
                  this._loadRawMaterials();
                  this._loadTransportEntries();
                  this._loadProduction(); 
                  this._loadSales();      
              },
      
              _loadRawMaterials: function () {
                  var chart = this.getView().getModel("chartModel");
                  this.getOwnerComponent().getModel().read("/ProjectdetailsSet", {
                      success: function (oData) {
                          var list = oData.results || [];
                          var map = {};
                          var grandTotal = 0;
      
                          list.forEach(function (r) {
                              var type = r.RawType || "Other";
                              var cost = parseFloat(r.CostPerUnit || 0);
                              grandTotal += cost;
                              if (!map[type]) map[type] = { RawType: type, TotalCost: 0 };
                              map[type].TotalCost += cost;
                          });
      
                          var finalArr = Object.keys(map).map(k => map[k]);
      
                          var graphPoints = finalArr.map(function (item) {
                              return {
                                  title: item.RawType,
                                  value: item.TotalCost,
                                  displayValue: item.TotalCost + " INR",
                                  color: item.TotalCost > 1000 ? "Error" : "Good" 
                              };
                          });
      
                          var donutData = finalArr.map(function(item) {
                              return {
                                  label: item.RawType,
                                  value: item.TotalCost,
                                  color: item.TotalCost > 2000 ? "Critical" : "Good"
                              };
                          });
      
                          chart.setProperty("/rawPoints", graphPoints);
                          chart.setProperty("/donutData", donutData);
                          chart.setProperty("/kpi/totalRaw", list.length);
                          chart.setProperty("/kpi/totalRawCost", grandTotal.toFixed(2));
                      }
                  });
              },
      
              _loadTransportEntries: function () {
                  var chart = this.getView().getModel("chartModel");
                  this.getOwnerComponent().getModel("transportModel").read("/TransTableSet", {
                      success: function (oData) {
                          var list = oData.results || [];
                          var trend = {};
                          var totalTransportCost = 0;
      
                          list.forEach(function (r) {
                              var d = convertSapDate(r.Eta);
                              var cost = parseFloat(r.Cost || 0);
                              totalTransportCost += cost;
                              if (!trend[d]) trend[d] = 0;
                              trend[d] += cost;
                          });
      
                          var trendArr = Object.keys(trend).sort().map(k => ({
                              label: k,
                              value: Number(trend[k]) || 0,
                              color: (Number(trend[k]) || 0) > 500 ? "Error" : "Good"
                          }));
      
                          chart.setProperty("/transportTrend", trendArr);
                          chart.setProperty("/kpi/totalTransport", list.length);
                          chart.setProperty("/kpi/transportCost", totalTransportCost.toFixed(2));
      chartModel.refresh(true);
                      }
                  });
              },
      
              _loadProduction: function() {
                  var chart = this.getView().getModel("chartModel");
                  this.getOwnerComponent().getModel("prodModel").read("/ProdOrderSet", {
                      success: function(oData) {
                          var list = oData.results || [];
                          var totalCost = 0;
                          var passCount = 0;
      
                          list.forEach(function(r){
                              totalCost += parseFloat(r.CostOfProduction || 0);
                              if(r.QcStatus && (r.QcStatus.toLowerCase().includes("pass") || r.QcStatus.toLowerCase().includes("success"))){
                                  passCount++;
                              }
                          });
      
                          var rate = list.length > 0 ? (passCount / list.length) * 100 : 0;
                          chart.setProperty("/kpi/totalProdCost", totalCost.toFixed(2));
                          chart.setProperty("/kpi/qcPassRate", Math.round(rate));
                      }
                  });
              },
      
              _loadSales: function() {
                  var chart = this.getView().getModel("chartModel");
                  this.getOwnerComponent().getModel("salesModel").read("/EndUserSet", {
                      success: function(oData) {
                          var list = oData.results || [];
                          var totalRev = 0;
                          var salesMap = {};
      
                          list.forEach(function(r){
                              var val = parseFloat(r.Price || 0);
                              totalRev += val;
                              var pid = r.ProdId || "Other";
                              if(!salesMap[pid]) salesMap[pid] = 0;
                              salesMap[pid] += val;
                          });
      
                          var salesArr = Object.keys(salesMap).map(k => ({
                              label: k,
                              value: salesMap[k],
                              color: "Good"
                          }));
      
                          chart.setProperty("/kpi/totalSales", totalRev.toFixed(2));
                          chart.setProperty("/kpi/salesCount", list.length);
                          chart.setProperty("/salesTrend", salesArr);
                      }
                  });
              },
      	cdsViewData: function () {this.getOwnerComponent().getRouter().navTo("FullView");},
              onViewRaw: function () { this.getOwnerComponent().getRouter().navTo("RawTable"); },
              onViewTransport: function () { this.getOwnerComponent().getRouter().navTo("TransportTable"); },
              onAddTransport: function () { this.getOwnerComponent().getRouter().navTo("TransportEntry"); },
              onProdPage: function () { this.getOwnerComponent().getRouter().navTo("ProdTable"); },
              onSalesPage: function () { this.getOwnerComponent().getRouter().navTo("SalesTable"); },
              
              onDonutSelect: function(oEvent) {
                   MessageToast.show(oEvent.getParameter("segment").getLabel());
              }
          });
      });

FullView.controller.js --> type --> application/x-javascript

      sap.ui.define([
          "sap/ui/core/mvc/Controller",
          "sap/ui/model/json/JSONModel"
      ], function (Controller, JSONModel) {
          "use strict";
      
          return Controller.extend("Z_UI5_RAW_DASHB.controller.FullView", {
      
              onInit: function () {
      
                  let oMainModel = this.getOwnerComponent().getModel(); // main OData service
                  let that = this;
      
                  oMainModel.read("/ProjectdetailsSet", {
                      success: function (oData) {
      
                          let rows = oData.results.map(function (r) {
      
                              return {
                                  // RAW
                                  RawId: r.RawId,
                                  RawDesc: r.RawDesc,
                                  RawType: r.RawType,
                                  RawCostFormatted: r.CostPerUnit + " " + r.RawCurrency,
      
                                  // PRODUCTION
                                  ProdQtyFormatted: r.QtyProduced + " " + r.ProdUom,
                                  ProdCostFormatted: r.CostOfProduction + " " + r.ProdCurrency,
                                  ProdDate: that._formatDate(r.ProdDate),
                                  QcStatus: r.QcStatus,
      
                                  // SALES
                                  SaleQtyFormatted: r.QtySold + " " + r.SaleUom,
                                  SalePriceFormatted: r.Price + " " + r.SaleCurrency,
                                  SaleDate: that._formatDate(r.SaleDate),
      
                                  // TRANSPORT
                                  TransMode: r.TransMode,
                                  DistanceFormatted: r.Distance + " " + r.TransUom,
                                  TransportCostFormatted: r.TransportCost + " " + r.TransCurrency,
                                  TransportEta: that._formatDate(r.TransportEta)
                              };
                          });
      
                          let oJSON = new JSONModel({ rows: rows });
                          that.getView().setModel(oJSON, "scm");
                      },
      
                      error: function (err) {
                          console.error("SCM Load Error", err);
                      }
                  });
              },
      
              _formatDate: function (value) {
          if (!value) return "";
      
          // case 1: SAP /Date(123456789)/
          let match = /Date\((\d+)\)/.exec(value);
          if (match) {
              let d = new Date(parseInt(match[1], 10));
              return d.toISOString().slice(0, 10);
          }
      
          // case 2: Already a Date object
          if (value instanceof Date) {
              return value.toISOString().slice(0, 10);
          }
      
          // case 3: Raw string (ISO or others)
          try {
              let d = new Date(value);
              if (!isNaN(d.getTime())) {
                  return d.toISOString().slice(0, 10);
              }
          } catch (e) {}
      
          return value;
      }
      
      
          });
      });

Login.controller.js --> type --> application/x-javascript

      sap.ui.define([
        "sap/ui/core/mvc/Controller"
      ], function (Controller) {
        "use strict";
      
        return Controller.extend("Z_UI5_RAW_DASHB.controller.Login", {
      
          onLogin: function () {
            var u = this.byId("user").getValue();
            var p = this.byId("pass").getValue();
      
            if (u === "" || p === "") {
              this.byId("msg").setText("❌ Enter both username & password");
              return;
            }
      
            // Simple local login
            if (u === "admin" && p === "admin") {
              this.getOwnerComponent().getRouter().navTo("Dashboard");
            } else {
              this.byId("msg").setText("❌ Invalid credentials");
            }
          }
      
        });
      });

ProdTable.controller.js --> type --> application/x-javascript

      sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "sap/ui/model/json/JSONModel"
      ], function (Controller, JSONModel) {
        "use strict";
      
        return Controller.extend("Z_UI5_RAW_DASHB.controller.ProdTable", {
      
          onInit: function () {
            var oModel = new sap.ui.model.odata.v2.ODataModel("/sap/opu/odata/sap/ZPROD_ORD_SRV/", {
              defaultBindingMode: "TwoWay",
              useBatch: false
            });
      
            var that = this;
      
            oModel.read("/ProdOrderSet", {
              success: function (oData) {
                var oJSON = new JSONModel({ ProdOrderSet: oData.results });
                that.getView().setModel(oJSON, "prodModel");
              },
              error: function (err) {
                console.error("Prod read error", err);
              }
            });
          },
      
          onSearch: function (oEvent) {
            var sQuery = oEvent.getParameter("newValue");
            var oBinding = this.byId("prodTable").getBinding("items");
      
            oBinding.filter([
              new sap.ui.model.Filter({
                filters: [
                  new sap.ui.model.Filter("ProdId", "Contains", sQuery),
                  new sap.ui.model.Filter("RawId", "Contains", sQuery)
                ],
                and: false
              })
            ]);
          }
      
        });
      });

RawTable.controller.js --> type --> application/x-javascript
      
      sap.ui.define([
        "sap/ui/core/mvc/Controller"
      ], function (Controller) {
        "use strict";
      
        return Controller.extend("Z_UI5_RAW_DASHB.controller.RawTable", {
      
          onInit: function () {},
      onAddTransport: function () {
                  this.getOwnerComponent().getRouter().navTo("TransportEntry");
              }
      
        });
      });

SalesTable.controller.js --> type --> application/x-javascript

      sap.ui.define([
        "sap/ui/core/mvc/Controller",
        "sap/ui/model/json/JSONModel"
      ], function (Controller, JSONModel) {
        "use strict";
      
        return Controller.extend("Z_UI5_RAW_DASHB.controller.SalesTable", {
      
          onInit: function () {
            var oModel = new sap.ui.model.odata.v2.ODataModel("/sap/opu/odata/sap/ZENDUSER_SVV_SRV/", {
              defaultBindingMode: "TwoWay",
              useBatch: false
            });
      
            var that = this;
      
            oModel.read("/EndUserSet", {
              success: function (oData) {
                var oJSON = new JSONModel({ EndUserSet: oData.results });
                that.getView().setModel(oJSON, "salesModel");
              },
              error: function (err) {
                console.error("Sales read error", err);
              }
            });
          },
      
          onSearch: function (oEvent) {
            var sQuery = oEvent.getParameter("newValue");
            var oBinding = this.byId("saleTable").getBinding("items");
      
            oBinding.filter([
              new sap.ui.model.Filter({
                filters: [
                  new sap.ui.model.Filter("SaleId", "Contains", sQuery),
                  new sap.ui.model.Filter("ProdId", "Contains", sQuery)
                ],
                and: false
              })
            ]);
          }
      
        });
      });

TransportEntry.controller.js --> type --> application/x-javascript

      sap.ui.define([
        "sap/ui/core/mvc/Controller"
      ], function (Controller) {
        "use strict";
      
        return Controller.extend("Z_UI5_RAW_DASHB.controller.TransportEntry", {
      
          onSubmit: function () {
            var m = this.getOwnerComponent().getModel("transportModel");
      
            var payload = {
              Mandt: "400",
              TransId: this.byId("transId").getValue(),
              RawId: this.byId("rawId").getValue(),
              TransMode: this.byId("transMode").getValue(),
              Distance: this.byId("distance").getValue(),
              UomDist: this.byId("uom").getValue(),
              Cost: this.byId("cost").getValue(),
              Waers: this.byId("waers").getValue(),
              Eta: this.byId("eta").getDateValue(),
              Status: this.byId("status").getValue(),
              CreatedBy: "TS410-20",
      	CreatedAt:"2025-11-27T12:30:45.0000000"
            };
      
            var that = this;
      
            m.create("/TransTableSet", payload, {
              success: function () {
                that.byId("msg").setText("✔ Successfully inserted!");
              },
              error: function (err) {
                that.byId("msg").setText("❌ Error: " + err.responseText);
              }
            });
          }
      
        });
      });

TransportTable.controller.js

        sap.ui.define([
          "sap/ui/core/mvc/Controller",
          "sap/ui/model/json/JSONModel"
        ], function (Controller, JSONModel) {
          "use strict";
        
          return Controller.extend("Z_UI5_RAW_DASHB.controller.TransportTable", {
        
            onInit: function () {
        
                    var oModel = this.getOwnerComponent().getModel("transportModel");
                    var that = this;
        
                    oModel.read("/TransTableSet", {
                        success: function (oData) {
                            var rows = oData.results || [];
        
                            rows.forEach(function (r) {
        
                                // FORMAT COST + CURRENCY
                                r.FormattedCost =
                                    (r.Waers || "") + " " + (parseFloat(r.Cost || 0).toFixed(2));
        
                                // FORMAT DISTANCE + UOM
                                r.FormattedDistance =
                                    (parseFloat(r.Distance || 0)) + " " + (r.UomDist || "");
        
                                // FORMAT ETA (ISO string)
                                if (r.Eta) {
                                    try {
                                        var dt = new Date(r.Eta);
                                        r.FormattedEta = dt.toISOString().slice(0, 10);
                                    } catch (e) {
                                        r.FormattedEta = r.Eta;
                                    }
                                } else {
                                    r.FormattedEta = "";
                                }
        
                                // CREATED AT
                                if (r.CreatedAt) {
                                    try {
                                        var d2 = new Date(r.CreatedAt);
                                        r.FormattedCreatedAt = d2.toISOString().slice(0, 10);
                                    } catch (e) {
                                        r.FormattedCreatedAt = r.CreatedAt;
                                    }
                                }
                            });
        
                            // SET JSON MODEL FOR TABLE
                            var oJSON = new JSONModel({ Rows: rows });
                            that.getView().setModel(oJSON, "tview");
                        },
        
                        error: function (err) {
                            console.log("Transport read error:", err);
                        }
                    });
                },
        
            // Manual refresh button in debug panel
            onRefreshDebug: function () {
              this.onInit();
            },
        
            // Safe formatters: tolerate undefined/null and various incoming formats
            formatDateSafe: function (value) {
              if (!value && value !== 0) return "";
              try {
                // handle /Date(123456789)/
                var m = /Date\((\d+)\)/i.exec(value);
                if (m) {
                  var dt = new Date(parseInt(m[1], 10));
                  return dt.toISOString().slice(0, 10);
                }
                // handle ISO strings
                var d = new Date(value);
                if (!isNaN(d.getTime())) return d.toISOString().slice(0, 10);
              } catch (e) { /* ignore */ }
              return String(value);
            },
        
            formatDistanceSafe: function (dist, uom) {
              if (dist === null || dist === undefined || dist === "") return "";
              // prefer raw string as-is, but add unit if present
              var s = String(dist);
              if (uom) s += " " + String(uom);
              return s;
            },
        
            formatCostSafe: function (cost, waers) {
              if (cost === null || cost === undefined || cost === "") return "";
              var s = String(cost);
              if (waers) {
                if (waers.toUpperCase() === "INR") s = "₹" + s;
                else s = s + " " + waers;
              }
              return s;
            },
        
            // rowJson: create a compact JSON string for the current binding context row
            rowJson: function () {
              // inside a cell formatter, 'this' is not the controller; we will attempt to read binding context
              // But easier: return placeholder — the debug main panel shows full JSON anyway.
              return "";
            }
        
          });
        
        });

App.view.xml --> type --> text/xml

        <mvc:View  controllerName="Z_UI5_RAW_DASHB.controller.App"  xmlns:mvc="sap.ui.core.mvc"  xmlns="sap.m">  <App id="app" /></mvc:View>

Dashboard.view.xml --> type --> text/xml

           <mvc:View  controllerName="Z_UI5_RAW_DASHB.controller.Dashboard"
          	xmlns:mvc="sap.ui.core.mvc"
          	xmlns="sap.m"
          	xmlns:f="sap.f"
          	xmlns:card="sap.f.cards"
          	xmlns:layout="sap.ui.layout"
          	xmlns:grid="sap.ui.layout.cssgrid"
          	xmlns:mc="sap.suite.ui.microchart">
          	<f:DynamicPage headerExpanded="true" preserveHeaderStateOnScroll="true" backgroundDesign="Solid">
          		<f:title>
          			<f:DynamicPageTitle>
          				<f:heading>
          					<Title text="Executive Dashboard" level="H2" />
          				</f:heading>
          				<f:expandedContent>
          					<Label text="Supply Chain 360° View" />
          				</f:expandedContent>
          				<f:actions>
          					<Button text="Add Transport Entry" type="Emphasized" icon="sap-icon://add"            press="onAddTransport" />
          					<!-- <Button text="CDS View" type="Emphasized" press="cdsViewData" /> -->
          				</f:actions>
          			</f:DynamicPageTitle>
          		</f:title>
          		<f:content>
          			<grid:CSSGrid id="grid1" gridTemplateColumns="repeat(auto-fit, minmax(360px, 1fr))"        gridGap="1rem">
          				<f:Card class="sapUiSmallMarginBottom" height="500px">
          					<f:header>
          						<card:NumericHeader              title="Raw Materials"              subtitle="Total Inventory Value"              number="{chartModel>/kpi/totalRawCost}"              unit="INR"              iconSrc="sap-icon://product"              sideIndicatorsAlignment="Begin">
          							<card:sideIndicators>
          								<card:NumericSideIndicator title="Items" number="{chartModel>/kpi/totalRaw}"                  unit="Qty" />
          								<card:NumericSideIndicator title="Types" number="{chartModel>/kpi/typesCount}"                  unit="Var" />
          							</card:sideIndicators>
          						</card:NumericHeader>
          					</f:header>
          					<f:content>
          						<VBox class="sapUiMediumPadding" height="100%" width="100%"              justifyContent="SpaceBetween" alignItems="Center">
          							<Title text="Cost Distribution" level="H5" />
          							<FlexBox height="220px" width="100%" alignItems="Center" justifyContent="Center">
          								<mc:InteractiveDonutChart selectionChanged="onDonutSelect"                  segments="{chartModel>/donutData}" width="100%">
          									<mc:segments>
          										<mc:InteractiveDonutChartSegment label="{chartModel>label}"                      value="{chartModel>value}" color="{chartModel>color}" />
          									</mc:segments>
          								</mc:InteractiveDonutChart>
          							</FlexBox>
          							<Button text="View Inventory" type="Ghost" width="100%" press="cdsViewData"                class="sapUiTinyMarginTop" />
          						</VBox>
          					</f:content>
          				</f:Card>
          				<f:Card class="sapUiSmallMarginBottom" height="500px">
          					<f:header>
          						<card:NumericHeader              title="Logistics Spend"              subtitle="Daily Trend Analysis"              number="{chartModel>/kpi/transportCost}"              unit="INR"              trend="{chartModel>/kpi/transportTrend}"              state="Critical"              iconSrc="sap-icon://shipping-status" />
          					</f:header>
          					<f:content>
          						<VBox class="sapUiMediumPadding" height="100%" width="100%" alignItems="Center"              justifyContent="SpaceBetween">
          							<Title text="Daily Cost History" level="H5" />
          							<FlexBox height="220px" width="100%" alignItems="Center" justifyContent="Center">
          								<mc:ColumnMicroChart                  width="300px"                  height="220px"                  columns="{chartModel>/transportTrend}"                  allowColumnLabels="true">
          									<mc:columns>
          										<mc:ColumnMicroChartData                      label="{chartModel>label}"                      value="{chartModel>value}"                      color="{chartModel>color}" />
          									</mc:columns>
          								</mc:ColumnMicroChart>
          							</FlexBox>
          							<Button text="Manage Logistics" type="Ghost" width="100%" press="onViewTransport"                class="sapUiMediumMarginTop" />
          						</VBox>
          					</f:content>
          				</f:Card>
          				<f:Card class="sapUiSmallMarginBottom" height="500px">
          					<f:header>
          						<card:NumericHeader              title="Expense Leaderboard"              subtitle="Top Costs by Material"              iconSrc="sap-icon://competitor"              number="Top 5"              unit="Items"              state="Neutral" />
          					</f:header>
          					<f:content>
          						<VBox class="sapUiMediumPadding" height="100%" width="100%" alignItems="Center"              justifyContent="SpaceBetween">
          							<Title text="Category Comparison" level="H5" />
          							<FlexBox height="220px" width="100%" fitContainer="true" justifyContent="Center">
          								<mc:ComparisonMicroChart height="100%" width="300px" data="{chartModel>/rawPoints}">
          									<mc:data>
          										<mc:ComparisonMicroChartData                      title="{chartModel>title}"                      value="{chartModel>value}"                      displayValue="{chartModel>displayValue}"                      color="{chartModel>color}" />
          									</mc:data>
          								</mc:ComparisonMicroChart>
          							</FlexBox>
          							<Button text="View Details" type="Ghost" width="100%" press="onViewRaw"                class="sapUiMediumMarginTop" />
          						</VBox>
          					</f:content>
          				</f:Card>
          				<f:Card class="sapUiSmallMarginBottom" height="500px">
          					<f:header>
          						<card:NumericHeader              title="Production Quality"              subtitle="Total Production Cost"              number="{chartModel>/kpi/totalProdCost}"              unit="INR"              state="Good"              iconSrc="sap-icon://factory" />
          					</f:header>
          					<f:content>
          						<VBox class="sapUiMediumPadding" height="100%" width="100%" alignItems="Center"              justifyContent="SpaceBetween">
          							<Title text="QC Pass Rate" level="H5" />
          							<FlexBox height="220px" width="100%" alignItems="Center" justifyContent="Center">
          								<mc:RadialMicroChart                  percentage="{chartModel>/kpi/qcPassRate}"                  valueColor="{= ${chartModel>/kpi/qcPassRate} > 70 ? 'Good' : 'Critical'}"                  height="180px"                  width="180px" />
          							</FlexBox>
          							<Button text="Production Logs" type="Ghost" width="100%" press="onProdPage" />
          						</VBox>
          					</f:content>
          				</f:Card>
          				<f:Card class="sapUiSmallMarginBottom" height="500px">
          					<f:header>
          						<card:NumericHeader              title="Sales Revenue"              subtitle="Total Orders: {chartModel>/kpi/salesCount}"              number="{chartModel>/kpi/totalSales}"              unit="INR"              state="Success"              iconSrc="sap-icon://sales-order" />
          					</f:header>
          					<f:content>
          						<VBox class="sapUiMediumPadding" height="100%" width="100%" alignItems="Center"              justifyContent="SpaceBetween">
          							<Title text="Best Selling Products" level="H5" />
          							<FlexBox height="220px" width="100%" alignItems="Center" justifyContent="Center">
          								<mc:ColumnMicroChart size="Responsive" height="300px" width="300px" columns="{chartModel>/salesTrend}"                  allowColumnLabels="true">
          									<mc:columns>
          										<mc:ColumnMicroChartData label="{chartModel>label}" value="{chartModel>value}"                      color="Good" />
          									</mc:columns>
          								</mc:ColumnMicroChart>
          							</FlexBox>
          							<Button text="View Sales" type="Ghost" width="100%" press="onSalesPage"                class="sapUiMediumMarginTop" />
          						</VBox>
          					</f:content>
          				</f:Card>
          				<f:Card class="sapUiSmallMarginBottom" height="500px">
          					<f:header>
          						<card:NumericHeader              title="Logistics Volume"              subtitle="Capacity Utilization"              number="{chartModel>/kpi/totalTransport}"              unit="Entries"              trend="{chartModel>/kpi/transportTrend}"              state="Critical"              iconSrc="sap-icon://shipping-status" />
          					</f:header>
          					<f:content>
          						<VBox class="sapUiMediumPadding" height="100%" width="100%" alignItems="Center"              justifyContent="SpaceBetween">
          							<Title text="Volume Analysis" level="H5" />
          							<FlexBox height="220px" width="100%" alignItems="Center" justifyContent="Center">
          								<mc:BulletMicroChart size="Responsive" height="300px" width="300px" scale="cnt" targetValue="100"                  forecastValue="110" showValueMarker="true">
          									<mc:actual>
          										<mc:BulletMicroChartData value="{chartModel>/kpi/totalTransport}"                      color="Critical" />
          									</mc:actual>
          									<mc:thresholds>
          										<mc:BulletMicroChartData value="0" color="Error" />
          										<mc:BulletMicroChartData value="50" color="Critical" />
          										<mc:BulletMicroChartData value="80" color="Good" />
          									</mc:thresholds>
          								</mc:BulletMicroChart>
          							</FlexBox>
          							<Button text="Transport Table" type="Ghost" width="100%" press="onViewTransport"                class="sapUiMediumMarginTop" />
          						</VBox>
          					</f:content>
          				</f:Card>
          			</grid:CSSGrid>
          		</f:content>
          	</f:DynamicPage>
          </mvc:View>

FullView.view.xml

          <mvc:View    controllerName="Z_UI5_RAW_DASHB.controller.FullView"
          	xmlns:mvc="sap.ui.core.mvc"
          	xmlns="sap.m">
          	<Page title="Complete Supply Chain Overview">
          		<content>
          			<Table                id="fullview"                growing="true"                growingScrollToLoad="true"                items="{scm>/rows}">
          				<headerToolbar>
          					<Toolbar>
          						<Title text="Complete Supply Chain Table" level="H2"/>
          						<ToolbarSpacer/>
          					</Toolbar>
          				</headerToolbar>
          				<columns>
          					<Column>
          						<Text text="Raw ID"/>
          					</Column>
          					<Column>
          						<Text text="Raw Desc"/>
          					</Column>
          					<Column>
          						<Text text="Type"/>
          					</Column>
          					<Column>
          						<Text text="Cost/Unit"/>
          					</Column>
          					<Column>
          						<Text text="Produced Qty"/>
          					</Column>
          					<Column>
          						<Text text="Prod Cost"/>
          					</Column>
          					<Column>
          						<Text text="Prod Date"/>
          					</Column>
          					<Column>
          						<Text text="QC"/>
          					</Column>
          					<Column>
          						<Text text="Sold Qty"/>
          					</Column>
          					<Column>
          						<Text text="Sale Price"/>
          					</Column>
          					<Column>
          						<Text text="Sale Date"/>
          					</Column>
          					<Column>
          						<Text text="Mode"/>
          					</Column>
          					<Column>
          						<Text text="Distance"/>
          					</Column>
          					<Column>
          						<Text text="Transport Cost"/>
          					</Column>
          					<Column>
          						<Text text="ETA"/>
          					</Column>
          				</columns>
          				<items>
          					<ColumnListItem>
          						<cells>
          							<Text text="{scm>RawId}" />
          							<Text text="{scm>RawDesc}" />
          							<Text text="{scm>RawType}" />
          							<Text text="{scm>RawCostFormatted}" />
          							<Text text="{scm>ProdQtyFormatted}" />
          							<Text text="{scm>ProdCostFormatted}" />
          							<Text text="{scm>ProdDate}" />
          							<Text text="{scm>QcStatus}" />
          							<Text text="{scm>SaleQtyFormatted}" />
          							<Text text="{scm>SalePriceFormatted}" />
          							<Text text="{scm>SaleDate}" />
          							<Text text="{scm>TransMode}" />
          							<Text text="{scm>DistanceFormatted}" />
          							<Text text="{scm>TransportCostFormatted}" />
          							<Text text="{scm>TransportEta}" />
          						</cells>
          					</ColumnListItem>
          				</items>
          			</Table>
          		</content>
          	</Page>
          </mvc:View>

Login.view.xml

            <mvc:View  controllerName="Z_UI5_RAW_DASHB.controller.Login"
            	xmlns:mvc="sap.ui.core.mvc"
            	xmlns="sap.m">
            	<Page title="Login">
            		<content>
            			<VBox class="sapUiSmallMargin">
            				<Input id="user" placeholder="Username" />
            				<Input id="pass" type="Password" placeholder="Password" />
            				<Button text="Login" press="onLogin" type="Emphasized" />
            				<Text id="msg" text="" />
            			</VBox>
            		</content>
            	</Page>
            </mvc:View>

ProdTable.view.xml

          <mvc:View    controllerName="Z_UI5_RAW_DASHB.controller.ProdTable"
          	xmlns:mvc="sap.ui.core.mvc"
          	xmlns="sap.m">
          	<Page title="Production Orders">
          		<content>
          			<VBox class="sapUiMediumMargin">
          				<SearchField          id="prodSearch"          liveChange="onSearch"          width="40%"          placeholder="Search Production ID or RawID"/>
          				<Table          id="prodTable"          inset="false"          growing="true"          items="{prodModel>/ProdOrderSet}">
          					<headerToolbar>
          						<Toolbar>
          							<Title text="Production Orders"/>
          						</Toolbar>
          					</headerToolbar>
          					<columns>
          						<Column>
          							<Text text="ProdId"/>
          						</Column>
          						<Column>
          							<Text text="RawId"/>
          						</Column>
          						<Column>
          							<Text text="Batch No"/>
          						</Column>
          						<Column>
          							<Text text="Qty Produced"/>
          						</Column>
          						<Column>
          							<Text text="Cost"/>
          						</Column>
          						<Column>
          							<Text text="Prod Date"/>
          						</Column>
          						<Column>
          							<Text text="Location"/>
          						</Column>
          						<Column>
          							<Text text="QC"/>
          						</Column>
          					</columns>
          					<items>
          						<ColumnListItem>
          							<cells>
          								<Text text="{prodModel>ProdId}"/>
          								<Text text="{prodModel>RawId}"/>
          								<Text text="{prodModel>BatchNo}"/>
          								<Text text="{prodModel>QtyProduced}"/>
          								<Text text="{prodModel>CostOfProduction}"/>
          								<Text text="{prodModel>ProdDate}"/>
          								<Text text="{prodModel>Location}"/>
          								<ObjectStatus text="{prodModel>QcStatus}" state="Success"/>
          							</cells>
          						</ColumnListItem>
          					</items>
          				</Table>
          			</VBox>
          		</content>
          	</Page>
          </mvc:View>

RawTable.view.xml

          <mvc:View controllerName="Z_UI5_RAW_DASHB.controller.RawTable"
          	xmlns:mvc="sap.ui.core.mvc"
          	xmlns="sap.m"
          	xmlns:layout="sap.ui.layout">
          	<Page id="page" title="Raw Material Dashboard">
          		<content>
          			<layout:VerticalLayout width="100%">
          				<SearchField id="search" width="30%" placeholder="Search RawId or Desc" liveChange="onSearch" />
          				<Button text="Add Transportation Entry" press="onAddTransport" type="Emphasized" />
          				<Table id="rawTable" inset="false" growing="true" items="{/ProjectdetailsSet}">
          					<headerToolbar>
          						<Toolbar>
          							<Title text="Raw Materials" level="H3"/>
          						</Toolbar>
          					</headerToolbar>
          					<columns>
          						<Column>
          							<Text text="RawId"/>
          						</Column>
          						<Column>
          							<Text text="RawDesc"/>
          						</Column>
          						<Column>
          							<Text text="RawType"/>
          						</Column>
          						<Column>
          							<Text text="RawUom"/>
          						</Column>
          						<Column>
          							<Text text="LeadTimeDays"/>
          						</Column>
          						<Column>
          							<Text text="CostPerUnit"/>
          						</Column>
          						<Column>
          							<Text text="SupplierId"/>
          						</Column>
          					</columns>
          					<items>
          						<ColumnListItem>
          							<cells>
          								<Text text="{RawId}" />
          								<Text text="{RawDesc}" />
          								<Text text="{RawType}" />
          								<Text text="{RawUom}" />
          								<Text text="{LeadTimeDays}" />
          								<Text text="{CostPerUnit}" />
          								<Text text="{SupplierId}" />
          							</cells>
          						</ColumnListItem>
          					</items>
          				</Table>
          			</layout:VerticalLayout>
          		</content>
          	</Page>
          </mvc:View>

SalesTable.view.xml

          <mvc:View    controllerName="Z_UI5_RAW_DASHB.controller.SalesTable"
          	xmlns:mvc="sap.ui.core.mvc"
          	xmlns="sap.m">
          	<Page title="Sales / End User Records">
          		<content>
          			<VBox class="sapUiMediumMargin">
          				<SearchField          id="saleSearch"          liveChange="onSearch"          width="40%"          placeholder="Search SaleId or ProdId"/>
          				<Table          id="saleTable"          inset="false"          growing="true"          items="{salesModel>/EndUserSet}">
          					<headerToolbar>
          						<Toolbar>
          							<Title text="Sales Entries"/>
          						</Toolbar>
          					</headerToolbar>
          					<columns>
          						<Column>
          							<Text text="SaleId"/>
          						</Column>
          						<Column>
          							<Text text="ProdId"/>
          						</Column>
          						<Column>
          							<Text text="Customer"/>
          						</Column>
          						<Column>
          							<Text text="Qty Sold"/>
          						</Column>
          						<Column>
          							<Text text="Price"/>
          						</Column>
          						<Column>
          							<Text text="Sale Date"/>
          						</Column>
          					</columns>
          					<items>
          						<ColumnListItem>
          							<cells>
          								<Text text="{salesModel>SaleId}"/>
          								<Text text="{salesModel>ProdId}"/>
          								<Text text="{salesModel>CustomerId}"/>
          								<Text text="{salesModel>QtySold}"/>
          								<Text text="{salesModel>Price}"/>
          								<Text text="{salesModel>SaleDate}"/>
          							</cells>
          						</ColumnListItem>
          					</items>
          				</Table>
          			</VBox>
          		</content>
          	</Page>
          </mvc:View>


TransportEntry.view.xml

          <mvc:View  controllerName="Z_UI5_RAW_DASHB.controller.TransportEntry"
          	xmlns:mvc="sap.ui.core.mvc"
          	xmlns="sap.m">
          	<Page title="Add Transport Entry">
          		<content>
          			<VBox class="sapUiSmallMargin">
          				<Input id="transId" placeholder="Transport ID" />
          				<Input id="rawId" placeholder="Raw ID" />
          				<Input id="transMode" placeholder="Transport Mode" />
          				<Input id="distance" placeholder="Distance" type="Number" />
          				<Input id="uom" placeholder="UOM" />
          				<Input id="cost" placeholder="Cost" type="Number" />
          				<Input id="waers" placeholder="Currency" />
          				<DatePicker id="eta" placeholder="ETA Date" />
          				<Input id="status" placeholder="Status" />
          				<Button text="Submit" press="onSubmit" type="Emphasized" />
          				<Text id="msg" />
          			</VBox>
          		</content>
          	</Page>
          </mvc:View>

TransportTable.view.xml

            <mvc:View  controllerName="Z_UI5_RAW_DASHB.controller.TransportTable"
            	xmlns:mvc="sap.ui.core.mvc"
            	xmlns="sap.m"  displayBlock="true">
            	<Page title="Transport Entries (Debug)">
            		<content>
            			<!-- DEBUG: show raw JSON of first entry so we can see actual property names -->
            			<!-- <Panel headerText="Debug: raw transportModel first entry" expandable="true" expanded="true"><VBox class="sapUiSmallPadding"><Text id="debugText" text="(loading...)" /><Button text="Refresh debug" press="onRefreshDebug" type="Transparent" /></VBox></Panel> -->
            			<!-- Final table -->
            			<Table id="transportTableFull" items="{tview>/Rows}" inset="false" growing="true" class="sapUiResponsiveMargin">
            				<headerToolbar>
            					<Toolbar>
            						<Title text="Transport Entries" level="H3" />
            					</Toolbar>
            				</headerToolbar>
            				<columns>
            					<Column>
            						<Text text="Transport ID" />
            					</Column>
            					<Column>
            						<Text text="Raw ID" />
            					</Column>
            					<Column>
            						<Text text="Mode" />
            					</Column>
            					<Column>
            						<Text text="Distance" />
            					</Column>
            					<Column>
            						<Text text="Cost" />
            					</Column>
            					<Column>
            						<Text text="ETA" />
            					</Column>
            				</columns>
            				<items>
            					<ColumnListItem>
            						<cells>
            							<Text text="{tview>TransId}" />
            							<Text text="{tview>RawId}" />
            							<Text text="{tview>TransMode}" />
            							<Text text="{tview>FormattedDistance}" />
            							<Text text="{tview>FormattedCost}" />
            							<Text text="{tview>FormattedEta}" />
            							<!-- use direct bindings to common names; if a field is undefined it will be blank -->
            							<!-- <Text text="{transportModel>TransId}" /><Text text="{transportModel>RawId}" /><Text text="{transportModel>TransMode}" /> -->
            							<!-- Distance + unit: use parts formatter -->
            							<!-- <Text text="{                parts: [                  'transportModel>Distance',                  'transportModel>UomDist'                ],                formatter: '.formatDistanceSafe'              }" /> -->
            							<!-- Cost + currency -->
            							<!-- <Text text="{                parts: [                  'transportModel>Cost',                  'transportModel>Waers'                ],                formatter: '.formatCostSafe'              }" /> -->
            							<!-- ETA formatted -->
            							<!-- <Text text="{ path: 'transportModel>Eta', formatter: '.formatDateSafe' }" /> -->
            							<!-- show raw JSON for this row (stringified) -->
            							<!-- <Text text="{path: 'transportModel', formatter: '.rowJson'}" /> -->
            						</cells>
            					</ColumnListItem>
            				</items>
            			</Table>
            		</content>
            	</Page>
            </mvc:View>

Component.js --> type --> text/js

            sap.ui.loader.config({
              async: true,
              preload: "off"
            });
            
            
            sap.ui.define([
              "sap/ui/core/UIComponent",
              "sap/ui/model/odata/v2/ODataModel"
            ], function (UIComponent, ODataModel) {
              "use strict";
            
              return UIComponent.extend("Z_UI5_RAW_DASHB.Component", {
            
                metadata: {
                  manifest: "json"
                },
            
                init: function () {
                  UIComponent.prototype.init.apply(this, arguments);
            
                  // initialize router
                  this.getRouter().initialize();
                }
              });
            });

index.html

          <!DOCTYPE html>
          <html>
          	<head>
          		<meta charset="utf-8" />
          		<title>Raw Material Dashboard</title>
          		<!-- SAPUI5 bootstrap from the local SAP UI5 runtime -->
          		<script      id="sap-ui-bootstrap"      src="/sap/public/bc/ui5_ui5/resources/sap-ui-core.js"      data-sap-ui-libs="sap.m,sap.ui.layout,sap.viz"      data-sap-ui-theme="sap_fiori_3"      data-sap-ui-async="true"      data-sap-ui-resourceroots='{"Z_UI5_RAW_DASHB": "./"}'></script>
          	</head>
          	<body class="sapUiBody" id="content">
          		<div id="uiArea"></div>
          		<script>      // bootstrap the Component      sap.ui.getCore().attachInit(function () {        new sap.ui.core.ComponentContainer({          name: "Z_UI5_RAW_DASHB",          settings: { id: "Z_UI5_RAW_DASHB" },          height: "100%"        }).placeAt("uiArea");      });    </script>
          	</body>
          </html>


manifest.json

          {
            "sap.app": {
              "id": "Z_UI5_RAW_DASHB",
              "type": "application",
              "i18n": "i18n/i18n.properties",
              "applicationVersion": {
                "version": "1.0.0"
              },
              "dataSources": {
                "mainService": {
                  "uri": "/sap/opu/odata/sap/ZANALYTIC_OV_SRV/",
                  "type": "OData",
                  "settings": {
                    "odataVersion": "2.0"
                  }
                },
                "transportService": {
                  "uri": "/sap/opu/odata/sap/ZTRANS_SRV_SRV_01/",
                  "type": "OData",
                  "settings": {
                    "odataVersion": "2.0"
                  }
                },
                "prodService": {
                  "uri": "/sap/opu/odata/sap/ZPROD_ORD_SRV/",
                  "type": "OData",
                  "settings": {
                    "odataVersion": "2.0"
                  }
                },
                "salesService": {
                  "uri": "/sap/opu/odata/sap/ZENDUSER_SVV_SRV/",
                  "type": "OData",
                  "settings": {
                    "odataVersion": "2.0"
                  }
                },
                "rawService": {
                  "uri": "/sap/opu/odata/sap/ZRAW_MAT_SRV_SRV/",
                  "type": "OData",
                  "settings": {
                    "odataVersion": "2.0"
                  }
                }
              }
            },
            "sap.ui": {
              "technology": "UI5"
            },
            "sap.ui5": {
              "rootView": {
                "viewName": "Z_UI5_RAW_DASHB.view.App",
                "type": "XML",
                "id": "rootView"
              },
              "dependencies": {
                "minUI5Version": "1.71.0",
                "libs": {
                  "sap.m": {},
                  "sap.ui.layout": {},
                  "sap.ui.core": {},
                  "sap.suite.ui.microchart": {},
                  "sap.f": {}
                }
              },
              "models": {
                "": {
                  "type": "sap.ui.model.odata.v2.ODataModel",
                  "dataSource": "mainService",
                  "settings": {
                    "useBatch": false
                  }
                },
                "transportModel": {
                  "type": "sap.ui.model.odata.v2.ODataModel",
                  "dataSource": "transportService",
                  "settings": {
                    "defaultBindingMode": "TwoWay",
                    "useBatch": false
                  }
                },
                "chartModel": {
                  "type": "sap.ui.model.json.JSONModel"
                },
                "prodModel": {
                  "type": "sap.ui.model.odata.v2.ODataModel",
                  "dataSource": "prodService",
                  "settings": {
                    "defaultBindingMode": "TwoWay",
                    "useBatch": false
                  }
                },
                "salesModel": {
                  "type": "sap.ui.model.odata.v2.ODataModel",
                  "dataSource": "salesService",
                  "settings": {
                    "defaultBindingMode": "TwoWay",
                    "useBatch": false
                  }
                },
                "rawModel": {
                  "type": "sap.ui.model.odata.v2.ODataModel",
                  "dataSource": "rawService",
                  "settings": {
                    "defaultBindingMode": "TwoWay",
                    "useBatch": false
                  }
                }
              },
              "routing": {
                "config": {
                  "routerClass": "sap.m.routing.Router",
                  "viewType": "XML",
                  "viewPath": "Z_UI5_RAW_DASHB.view",
                  "controlAggregation": "pages",
                  "controlId": "app"
                },
                "routes": [
                  {
                    "pattern": "",
                    "name": "Login",
                    "target": "Login"
                  },
                  {
                    "pattern": "dashboard",
                    "name": "Dashboard",
                    "target": "Dashboard"
                  },
                  {
                    "pattern": "raw",
                    "name": "RawTable",
                    "target": "RawTable"
                  },
                  {
                    "pattern": "transport",
                    "name": "TransportTable",
                    "target": "TransportTable"
                  },
                  {
                    "pattern": "TransportEntry",
                    "name": "TransportEntry",
                    "target": "TransportEntry"
                  },
                  {
                    "pattern": "production",
                    "name": "ProdTable",
                    "target": "ProdTable"
                  },
                  {
                    "pattern": "sales",
                    "name": "SalesTable",
                    "target": "SalesTable"
                  },
                  {
                    "pattern": "fullview",
                    "name": "FullView",
                    "target": "FullView"
                  }
                ],
                "targets": {
                  "App": {
                    "viewName": "App"
                  },
                  "Login": {
                    "viewName": "Login"
                  },
                  "Dashboard": {
                    "viewName": "Dashboard"
                  },
                  "RawTable": {
                    "viewName": "RawTable"
                  },
                  "TransportTable": {
                    "viewName": "TransportTable"
                  },
                  "TransportEntry": {
                    "viewName": "TransportEntry"
                  },
                  "ProdTable": {
                    "viewName": "ProdTable"
                  },
                  "SalesTable": {
                    "viewName": "SalesTable"
                  },
                  "FullView": {
                    "viewName": "FullView"
                  }
                }
              }
            }
          }



CDS View --> Analytic Dashboard --> Type --> Core Definition

        @AbapCatalog.sqlViewName: 'ZVSC_FULL_SC'
        @AbapCatalog.compiler.compareFilter: true
        @AbapCatalog.preserveKey: true
        @AccessControl.authorizationCheck: #NOT_REQUIRED
        @EndUserText.label: 'Full Supply Chain Analytics View'
        @Metadata.ignorePropagatedAnnotations: true
        
        define view ZSC_FULL_SUPPLY_CHAIN
          as select from zraw_mat        as raw
        
            left outer join ztrans       as trans
              on  trans.mandt  = raw.mandt
              and trans.raw_id = raw.raw_id
        
            left outer join zprod_order  as prod
              on  prod.mandt  = raw.mandt
              and prod.raw_id = raw.raw_id
        
            left outer join zenduser     as sale
              on  sale.mandt  = prod.mandt
              and sale.prod_id = prod.prod_id
        {
          // RAW MATERIAL FIELDS
          raw.raw_id,
          raw.raw_desc,
          raw.raw_type,
          raw.uom              as raw_uom,
          raw.lead_time_days,
          raw.cost_per_unit,
          raw.waers            as raw_currency,
          raw.supplier_id,
          raw.status           as raw_status,
          raw.created_by       as raw_created_by,
          raw.created_at       as raw_created_at,
        
          // TRANSPORT FIELDS
          trans.trans_id,
          trans.trans_mode,
          trans.distance,
          trans.uom_dist       as trans_uom,
          trans.cost           as transport_cost,
          trans.waers          as trans_currency,
          trans.eta            as transport_eta,
          trans.status         as trans_status,
        
          // PRODUCTION FIELDS
          prod.prod_id,
          prod.batch_no,
          prod.qty_produced,
          prod.uom_qty         as prod_uom,
          prod.cost_of_production,
          prod.waers           as prod_currency,
          prod.prod_date,
          prod.location,
          prod.qc_status,
          prod.status          as prod_status,
        
          // SALES FIELDS
          sale.sale_id,
          sale.customer_id,
          sale.qty_sold,
          sale.uom_qty         as sale_uom,
          sale.sale_date,
          sale.delivery_trans_id,
          sale.price,
          sale.waers           as sale_currency,
          sale.status          as sale_status,
          sale.created_by      as sale_created_by,
          sale.created_at      as sale_created_at
        }

ZSC_FULL_SC_CUBE

        @AbapCatalog.compiler.compareFilter: true
        @AbapCatalog.preserveKey: true
        @AccessControl.authorizationCheck: #NOT_REQUIRED
        @EndUserText.label: 'Supply Chain Analytical Cube'
        @Metadata.ignorePropagatedAnnotations: true
        @Analytics.dataCategory: #CUBE
        @VDM.viewType: #COMPOSITE
        @AbapCatalog.sqlViewName: 'ZVSC_SC_CUBE'
        
        define view ZSC_FULL_SC_CUBE
          as select from ZSC_FULL_SUPPLY_CHAIN
        {
          // Dimensions
          raw_id,
          raw_desc,
          raw_type,
          prod_id,
          batch_no,
          customer_id,
          trans_mode,
          location,
          qc_status,
          sale_status,
        
          // Measures
          qty_produced,
          qty_sold,
          distance,
          cost_of_production,
          transport_cost,
          price
        }

ZSC_FULL_SC_CONS

        @AbapCatalog.sqlViewName: 'ZSC_CSP_VW'
        @AbapCatalog.compiler.compareFilter: true
        @AbapCatalog.preserveKey: true
        @AccessControl.authorizationCheck: #NOT_REQUIRED
        @EndUserText.label: 'Supply Chain Consumption View'
        @Metadata.ignorePropagatedAnnotations: true
        @Analytics.query: true
        @VDM.viewType: #CONSUMPTION
        
        define view ZSC_FULL_SC_CONS
          as select from ZSC_FULL_SC_CUBE
        {
          key raw_id,
          raw_desc,
          raw_type,
          prod_id,
          customer_id,
          trans_mode,
          location,
          qc_status,
        
          qty_produced,
          qty_sold,
          distance,
          cost_of_production,
          transport_cost,
          price
        }


ABAP Database Data Entry through Dynpro Screen and Displaying it through ALV GRID and Adobe Forms
* Go to SE38
* Create a programme

            *&---------------------------------------------------------------------*
            *& Report ZPROD_ORDER_APP
            *&---------------------------------------------------------------------*
            *&
            *&---------------------------------------------------------------------*
            REPORT ZPROD_ORDER_APP.
            
            *---------------------------------------------------------------------
            * This program expects:
            * - DDIC table zprod_order (structure zprod_order)
            * - Two screens: 100 (form) and 200 (ALV display)
            * - Custom control on screen 200 named CC_ALV
            * - Smartform name ZPROD_ORDER_SF (optional)
            * - Adobe form name Z_ADOBE_PROD (optional)
            *---------------------------------------------------------------------
            
            TABLES: ZPROD_ORDER.
            
            " Local types & vars
            TYPES: BEGIN OF TY_PROD,
                     MANDT              TYPE ZPROD_ORDER-MANDT,
                     PROD_ID            TYPE ZPROD_ORDER-PROD_ID,
                     RAW_ID             TYPE ZPROD_ORDER-RAW_ID,
                     BATCH_NO           TYPE ZPROD_ORDER-BATCH_NO,
                     QTY_PRODUCED       TYPE ZPROD_ORDER-QTY_PRODUCED,
                     UOM_QTY            TYPE ZPROD_ORDER-UOM_QTY,
                     COST_OF_PRODUCTION TYPE ZPROD_ORDER-COST_OF_PRODUCTION,
                     WAERS              TYPE ZPROD_ORDER-WAERS,
                     PROD_DATE          TYPE ZPROD_ORDER-PROD_DATE,
                     LOCATION           TYPE ZPROD_ORDER-LOCATION,
                     QC_STATUS          TYPE ZPROD_ORDER-QC_STATUS,
                     STATUS             TYPE ZPROD_ORDER-STATUS,
                     CREATED_BY         TYPE ZPROD_ORDER-CREATED_BY,
                     CREATED_AT         TYPE ZPROD_ORDER-CREATED_AT,
                   END OF TY_PROD.
            
            DATA: GS_PROD TYPE ZPROD_ORDER.
            DATA LV_TS TYPE TIMESTAMPL.
            DATA: GT_PROD TYPE STANDARD TABLE OF ZPROD_ORDER WITH DEFAULT KEY.
            DATA: OK_CODE TYPE SY-UCOMM.
            
            " ALV objects
            DATA: GO_CONTAINER TYPE REF TO CL_GUI_CUSTOM_CONTAINER,
                  GO_GRID      TYPE REF TO CL_GUI_ALV_GRID,
                  GD_GRID_ID   TYPE SCRFNAME VALUE 'CC_ALV'.
            
            DATA: GV_ALV_INITIALIZED TYPE ABAP_BOOL VALUE ABAP_FALSE.
            
            *---------------------------------------------------------------------*
            * SCREEN 100
            *---------------------------------------------------------------------*
            MODULE STATUS_0100_OUTPUT OUTPUT.
              SET PF-STATUS 'STATUS100'.
            ENDMODULE.
            
            *MODULE user_command_0100 INPUT.
            *  MESSAGE 'PAI TRIGGERED' TYPE 'I'.
            *ENDMODULE.
            
            MODULE USER_COMMAND_0100 INPUT.
              CASE SY-UCOMM.
                WHEN 'SAVE'.
                  BREAK-POINT.
                  PERFORM SAVE_PROD.
                WHEN 'UPDATE'.
                  PERFORM UPDATE_PROD.
                WHEN 'PRINT_SMART'.
                  PERFORM PRINT_SMARTFORM.
                WHEN 'PRINT_ADOBE'.
                  PERFORM PRINT_ADOBE.
                WHEN 'DISP'.
                  PERFORM LOAD_PROD_TABLE.
                  gv_alv_initialized = abap_false.  "reset for new screen load
                  CALL SCREEN 200.
              ENDCASE.
            ENDMODULE.
            
            *---------------------------------------------------------------------*
            * SCREEN 200
            *---------------------------------------------------------------------*
            MODULE STATUS_0200_OUTPUT OUTPUT.
              DATA: LT_FCAT TYPE LVC_T_FCAT.
              SET PF-STATUS 'STATUS200'.   " <-- NEW FIX
            
              PERFORM LOAD_PROD_TABLE. "new changes
              IF GV_ALV_INITIALIZED = ABAP_FALSE.
                PERFORM INIT_ALV.
                GV_ALV_INITIALIZED = ABAP_TRUE.
              ELSE.
                " If ALV already initialized, just refresh its data (efficient path)
                IF NOT GO_GRID IS INITIAL.
                  " Update the ALV outtab and refresh display
                  CALL METHOD GO_GRID->SET_TABLE_FOR_FIRST_DISPLAY
                    EXPORTING
                      I_STRUCTURE_NAME = 'ZPROD_ORDER'
                    CHANGING
                      IT_OUTTAB        = GT_PROD
                      IT_FIELDCATALOG  = LT_FCAT.
            
                  CALL METHOD GO_GRID->REFRESH_TABLE_DISPLAY( ).
                ENDIF.
              ENDIF.
            ENDMODULE.
            
            MODULE USER_COMMAND_0200 INPUT.
              CASE SY-UCOMM.
                WHEN 'ALV_SAVE'.
                  CLEAR GS_PROD.
                  CALL SCREEN 100.
                  PERFORM LOAD_PROD_TABLE.
                  GO_GRID->REFRESH_TABLE_DISPLAY( ).
            
                WHEN 'ALV_UPDATE'.
                  PERFORM ALV_UPDATE_SELECTED.
                  PERFORM LOAD_PROD_TABLE.
                  GO_GRID->REFRESH_TABLE_DISPLAY( ).
            
                WHEN 'REFRESH'.
                  PERFORM LOAD_PROD_TABLE.
                  GO_GRID->REFRESH_TABLE_DISPLAY( ).
            
                WHEN 'ALV_BACK'.
                  LEAVE TO SCREEN 0.
              ENDCASE.
            ENDMODULE.
            
            *---------------------------------------------------------------------*
            * FORM SAVE
            *---------------------------------------------------------------------*
            FORM SAVE_PROD.
              GS_PROD-MANDT = SY-MANDT.
              GET TIME STAMP FIELD LV_TS.
              GS_PROD-CREATED_AT = LV_TS.
            
              IF GS_PROD-PROD_ID IS INITIAL.
                MESSAGE 'Prod ID required' TYPE 'E'.
                RETURN.
              ENDIF.
            
              INSERT ZPROD_ORDER FROM GS_PROD.
              IF SY-SUBRC = 0.
                MESSAGE 'Record saved' TYPE 'S'.
              ELSE.
                MESSAGE 'Insert failed (maybe exists)' TYPE 'E'.
              ENDIF.
            ENDFORM.
            
            *---------------------------------------------------------------------*
            * FORM UPDATE
            *---------------------------------------------------------------------*
            FORM UPDATE_PROD.
              IF GS_PROD-PROD_ID IS INITIAL.
                MESSAGE 'Prod ID required for update' TYPE 'E'.
                RETURN.
              ENDIF.
            
              UPDATE ZPROD_ORDER FROM GS_PROD.
              IF SY-SUBRC = 0.
                MESSAGE 'Record updated' TYPE 'S'.
              ELSE.
                MESSAGE 'Update failed (not found)' TYPE 'E'.
              ENDIF.
            ENDFORM.
            
            *---------------------------------------------------------------------*
            * FORM LOAD TABLE
            *---------------------------------------------------------------------*
            FORM LOAD_PROD_TABLE.
              SELECT * FROM ZPROD_ORDER INTO TABLE GT_PROD UP TO 100 ROWS.
            ENDFORM.
            
            *---------------------------------------------------------------------*
            * FORM INIT ALV (CORRECTED – REMOVE REUSE FM)
            *---------------------------------------------------------------------*
            FORM INIT_ALV.
              CALL METHOD cl_gui_cfw=>flush.
              DATA: LS_FCAT TYPE LVC_S_FCAT,
                    LT_FCAT TYPE LVC_T_FCAT.
            
              IF GO_CONTAINER IS INITIAL.
                CREATE OBJECT GO_CONTAINER
                  EXPORTING
                    CONTAINER_NAME = GD_GRID_ID.
              ENDIF.
            
              IF GO_GRID IS INITIAL.
                CREATE OBJECT GO_GRID
                  EXPORTING
                    I_PARENT = GO_CONTAINER.
              ENDIF.
            
              "--- FIELD CATALOG SAME AS YOUR ORIGINAL CODE ---
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'PROD_ID'.
              LS_FCAT-COLTEXT   = 'Production ID'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'RAW_ID'.
              LS_FCAT-COLTEXT   = 'Raw Material'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'BATCH_NO'.
              LS_FCAT-COLTEXT   = 'Batch Number'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'QTY_PRODUCED'.
              LS_FCAT-COLTEXT   = 'Quantity Produced'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'UOM_QTY'.
              LS_FCAT-COLTEXT   = 'UoM'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'COST_OF_PRODUCTION'.
              LS_FCAT-COLTEXT   = 'Cost of Production'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'WAERS'.
              LS_FCAT-COLTEXT   = 'Currency'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'PROD_DATE'.
              LS_FCAT-COLTEXT   = 'Production Date'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'LOCATION'.
              LS_FCAT-COLTEXT   = 'Location'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'QC_STATUS'.
              LS_FCAT-COLTEXT   = 'QC Status'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'STATUS'.
              LS_FCAT-COLTEXT   = 'Status'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'CREATED_BY'.
              LS_FCAT-COLTEXT   = 'Created By'.
              APPEND LS_FCAT TO LT_FCAT.
            
              CLEAR LS_FCAT.
              LS_FCAT-FIELDNAME = 'CREATED_AT'.
              LS_FCAT-COLTEXT   = 'Created At'.
              APPEND LS_FCAT TO LT_FCAT.
            
              "--- LOAD TABLE ---
              PERFORM LOAD_PROD_TABLE.
            
              "--- NEW FIX: Replace REUSE ALV with GRID METHOD ---
              CALL METHOD GO_GRID->SET_TABLE_FOR_FIRST_DISPLAY
                EXPORTING
                  I_STRUCTURE_NAME = 'ZPROD_ORDER'
                  IS_VARIANT       = VALUE DISVARIANT( REPORT = SY-REPID )
                CHANGING
                  IT_OUTTAB        = GT_PROD
                  IT_FIELDCATALOG  = LT_FCAT.
            
              CALL METHOD GO_GRID->SET_READY_FOR_INPUT
                EXPORTING
                  I_READY_FOR_INPUT = 1.
            
            ENDFORM.
            
            *---------------------------------------------------------------------*
            * FORM: Update selected row
            *---------------------------------------------------------------------*
            FORM ALV_UPDATE_SELECTED.
              DATA: LS_ROW TYPE ZPROD_ORDER,
                    LT_SEL TYPE LVC_T_ROW,
                    LS_SEL TYPE LVC_S_ROW,
                    LV_ROW TYPE I.
            
              CALL METHOD GO_GRID->GET_SELECTED_ROWS
                IMPORTING
                  ET_INDEX_ROWS = LT_SEL.
            
              IF LT_SEL IS INITIAL.
                MESSAGE 'Select a row in ALV to update' TYPE 'I'.
                RETURN.
              ENDIF.
            
              READ TABLE LT_SEL INDEX 1 INTO LS_SEL.
              LV_ROW = LS_SEL-INDEX.
            
              IF LV_ROW IS INITIAL.
                MESSAGE 'Unable to resolve selected row index' TYPE 'E'.
                RETURN.
              ENDIF.
            
              READ TABLE GT_PROD INDEX LV_ROW INTO LS_ROW.
              MOVE-CORRESPONDING LS_ROW TO GS_PROD.
            
              CALL SCREEN 100.
            ENDFORM.
            
            *---------------------------------------------------------------------*
            * PRINT SMARTFORM
            *---------------------------------------------------------------------*
            FORM PRINT_SMARTFORM.
              MESSAGE 'Smartform placeholder' TYPE 'I'.
            ENDFORM.
            
            *---------------------------------------------------------------------*
            * PRINT ADOBE
            *---------------------------------------------------------------------*
            FORM PRINT_ADOBE.
              DATA: lv_fp_name      TYPE fpname VALUE 'Z_ADOBE_PROD_ORDER',
              lv_fm_name      TYPE funcname,
              lo_pdfobj       TYPE fpformoutput,
              ls_docparams    TYPE sfpdocparams,
              ls_outparams    TYPE sfpoutputparams,
              lv_pdf_xstring  TYPE xstring,
              lt_pdf_binary   TYPE solix_tab,
              lv_pdf_length   TYPE so_obj_len,
        *     ls_docoutput    TYPE /1BCDWB/DOCPARAMSOUTPUT,
              lv_filename     TYPE string.
        
        *--------------------------------------------------------------
        * 1. GET ADOBE FUNCTION MODULE NAME
        *--------------------------------------------------------------
        CALL FUNCTION 'FP_FUNCTION_MODULE_NAME'
          EXPORTING
            i_name     = lv_fp_name
          IMPORTING
            e_funcname = lv_fm_name
          EXCEPTIONS
            OTHERS     = 1.
        
        IF sy-subrc <> 0.
          MESSAGE 'Adobe FM not found' TYPE 'E'.
          EXIT.
        ENDIF.
        
        *--------------------------------------------------------------
        * 2. OPEN ADOBE JOB
        *--------------------------------------------------------------
        CALL FUNCTION 'FP_JOB_OPEN'
          CHANGING
            ie_outputparams = ls_outparams
          EXCEPTIONS
            OTHERS          = 1.
        
        ls_docparams-langu = sy-langu.
        
        *--------------------------------------------------------------
        * 3. CALL THE GENERATED ADOBE FM
        *     - IMPORTANT: parameter name MUST match your Interface!
        *     - You MUST have GS_PROD defined in the interface
        *--------------------------------------------------------------
        CALL FUNCTION lv_fm_name
          EXPORTING
            /1bcdwb/docparams = ls_docparams
            GS_PROD           = gs_prod
          IMPORTING
            /1bcdwb/formoutput = lo_pdfobj
        *   /1BCDWB/DOCOUTPUT = ls_docoutput
          EXCEPTIONS
            OTHERS            = 1.
        
        *--------------------------------------------------------------
        * 4. CLOSE JOB
        *--------------------------------------------------------------
        CALL FUNCTION 'FP_JOB_CLOSE'
          EXCEPTIONS
            OTHERS = 1.

            ENDFORM.
            
            *---------------------------------------------------------------------*
            * INITIALIZATION / START
            *---------------------------------------------------------------------*
            INITIALIZATION.
              GET TIME STAMP FIELD LV_TS.
            
              GS_PROD-CREATED_BY = SY-UNAME.
              GS_PROD-CREATED_AT = LV_TS.
            
            START-OF-SELECTION.
              CALL SCREEN 100.

* <img width="1887" height="994" alt="image" src="https://github.com/user-attachments/assets/36a1d080-cf84-42f0-833f-e1e22a4f9f9f" />
<img width="1884" height="991" alt="image" src="https://github.com/user-attachments/assets/e4d17b61-49ee-4696-88dc-1b8f91143dff" />

Screen 100 --> Layout
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/5ab8e2d3-e989-4686-8b4a-03655fc33077" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/3146fde0-c014-45ae-9ae4-c087a54a0662" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/011332a2-a86d-42e2-8fab-44db79ee503e" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/90f43ed8-8aa3-4b37-b4bf-4f11ad3490b1" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/08b9e3f9-ba7a-43cb-b303-92a042816086" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/b3b116d3-c812-47fd-846a-bc957debe442" />















