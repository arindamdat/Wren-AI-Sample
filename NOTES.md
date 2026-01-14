1. all the DateTime and Date properties under nested "field" object should be "varchar" in the __schema table.
2. The definition of the VIEW will be like below:
CREATE OR REPLACE VIEW iceberg.reporting.documents_view AS 
    SELECT _id AS id, TRY_CAST(createdOn AS TIMESTAMP(6)) as createdOn, createdById, TRY_CAST(modifiedOn AS TIMESTAMP(6)) AS modifiedOn, modifiedById, isDeleted, collectionId, 
    isProcessDocument, fields.NAME.value AS name, TRY_CAST(from_iso8601_timestamp(fields.PROCESSEDDATE.value) AS TIMESTAMP(6)) AS processedDate, 
    fields.SEQUENCENR.value AS sequenceNo, fields.SUBJECT.value AS subject, fields.PROCESSED.value AS isProcessed 
    FROM mongodb.rms1.documents;
3. users_vew definition
CREATE OR REPLACE VIEW iceberg.reporting.users_view AS 
  SELECT _id as id, name as fullName, email, firstName, lastName FROM mongodb.rms1.users

4. user_roles_view definition
CREATE OR REPLACE VIEW iceberg.reporting.user_roles_view AS 
 SELECT u._id AS userId,
 r_id AS roleId,
 r_code AS roleCode,
 r_type AS roleType,
 r_name AS roleName,
 r_is_admin AS isAdminRole 
  FROM mongodb.rms1.users u 
   CROSS JOIN UNNEST(u.roles) AS t(
    r_id,
    r_code,
    r_type,
    r_name,
    r_is_admin,
    r_priv_obj
   )