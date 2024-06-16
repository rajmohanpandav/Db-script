CREATE PROCEDURE [dbo].[sp_Show1_RECOMPILE]  
  
 as  
  
SELECT  'use '+  DB_NAME(st.dbid) +'  EXEC SP_RECOMPILE '+(OBJECT_NAME(st.objectid, st.dbid)) [Recompile_Stmt],  
COALESCE( QUOTENAME(DB_NAME(st.dbid)) + N'.' + QUOTENAME(OBJECT_SCHEMA_NAME(st.objectid, st.dbid)) + N'.' + QUOTENAME(OBJECT_NAME(st.objectid, st.dbid)),'') AS command_text,  
count(1) as [No of session]  
FROM sys.dm_exec_sessions AS s  
INNER JOIN sys.dm_exec_requests AS r ON r.session_id = s.session_id  
CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) AS st  
WHERE r.session_id != @@SPID and program_name like '%.Net SqlClient Data Provider%'  
group by  (OBJECT_NAME(st.objectid, st.dbid)),DB_NAME(st.dbid) ,COALESCE( QUOTENAME(DB_NAME(st.dbid)) + N'.' + QUOTENAME(OBJECT_SCHEMA_NAME(st.objectid, st.dbid)) + N'.' + QUOTENAME(OBJECT_NAME(st.objectid, st.dbid)),'')  
ORDER BY count(1) desc;
