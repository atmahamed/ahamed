create table Employee(
  EmployeeID int not null identity(1,1) primary key,
  FirstName varchar(30),
  LastName varchar(30),
  Mobile varchar(13), 
  Email varchar(100),
  Address1 varchar(50),
  Address2 varchar(50),
  City varchar(30),
  State varchar(30),
  CreatedOn datetime,
  ModifiedOn datetime
 )
 
 
 create table EmployeeReference(
  EmployeeReferenceID int not null identity(1,1) primary key,
  EmployeeID int foreign key references Employee(EmployeeID),
  FirstName varchar(30),
  LastName varchar(30),
  Mobile varchar(13), 
  Email varchar(100),
  Address1 varchar(50),
  Address2 varchar(50),
  City varchar(30),
  State varchar(30),
  CreatedOn datetime,
  ModifiedOn datetime
 )
 
 Create table EmployeeEmploymentInfo(
   EmployeeCurrentInfo int not null identity(1,1) primary key,
   EmployeeID int foreign key references Employee(EmployeeID),
   CompanyName varchar(255),
   Location varchar(50),
   FromDate datetime,
   ToDate datetime,
   Salary money,
   IsCurrentCompany bit,
   CreatedOn datetime,
   ModifiedOn datetime,
   )
 


Create table CompanyType(
   CompanyTypeID int not null identity(1,1) primary key,
   Name varchar(30),
   CreatedOn datetime,
   ModifiedOn datetime
   )
 
 
 Create table FoodType(
   FoodTypeID int not null identity(1,1) primary key,
   Name varchar(30),
   CreatedOn datetime,
   ModifiedOn datetime
   )
   
   Create table RoomType(
   RoomTypeID int not null identity(1,1) primary key,
   Name varchar(30),
   CreatedOn datetime,
   ModifiedOn datetime
   )
   
create table Employer(
  EmployerID int not null identity(1,1) primary key,
  CompanyName varchar(255),
  CompanyTypeID int foreign key references CompanyType(CompanyTypeID),
  OtherCompanyType varchar(30),
  Mobile varchar(13), 
  Landline varchar(30),  
  Email varchar(100),
  Address1 varchar(50),
  Address2 varchar(50),
  City varchar(30),
  State varchar(30),
  FromWorkingHours datetime,
  ToWorkingHours datetime,
  NumberOfWorkingHours int,
  FoodTypeID int foreign key references FoodType(FoodTypeID),
  RoomTypeID int foreign key references RoomType(RoomTypeID),
  CreatedOn datetime,
  ModifiedOn datetime
 )
 

Create table PostedJobs(
  PostedJobID int not null identity(1,1) primary key,
  EmployerID int foreign key references Employer(EmployerID),
  EmployeeDesignation varchar(50),
  Description varchar(max),
  Salary money,
  PostedDate datetime,
  Active bit,
  CreatedOn datetime,
  ModifiedOn datetime
)


create table AppliedJobs(
  AppliedJobID int not null identity(1,1) primary key,
  PostedJobID int not null,
  EmployeeID int not null,
  EmployerID int not null,
  AppliedDate datetime,
  CreatedOn datetime,
  ModifiedOn datetime
  )



create procedure CreateEmployee(
  @MODE VARCHAR(1),
  @EmployeeID INT = NULL,
  @FirstName varchar(30),
  @LastName varchar(30),
  @Mobile varchar(13), 
  @Email varchar(100),
  @Address1 varchar(50),
  @Address2 varchar(50),
  @City varchar(30),
  @State varchar(30),
  @RefFirstName varchar(30),
  @RefLastName varchar(30),
  @RefMobile varchar(13), 
  @RefEmail varchar(100),
  @RefAddress1 varchar(50),
  @RefAddress2 varchar(50),
  @RefCity varchar(30),
  @RefState varchar(30)
)
  
  AS
  BEGIN
  
  IF @MODE = 'A'
    BEGIN
      INSERT INTO Employee(FirstName,LastName,Mobile,Email,Address1,Address2,City,State, CreatedOn) VALUES
      (@FirstName,@LastName,@Mobile,@Email,@Address1,@Address2,@City,@State, GETDATE())
      
      SET @EmployeeID = SELECT SCOPE_IDENTITY();
      
      INSERT INTO EmployeeReference(FirstName,LastName,Mobile,Email,Address1,Address2,City,State, CreatedOn) VALUES
      (@RefFirstName,@RefLastName,@RefMobile,@RefEmail,@RefAddress1,@RefAddress2,@RefCity,@RefState, GETDATE())
    END
  ELSE IF @MODE = 'E'
    BEGIN
      UPDATE Employee SET FirstName = @FirstName,LastName=@LastName,Mobile=@Mobile,
      Email=@Email,Address1=@Address1,Address2=@Address2,City=@City,State=@State, ModifiedOn = GETDATE()
      WHERE EmployeeID = @EmployeeID
      
      UPDATE EmployeeReference SET FirstName = @RefFirstName,LastName=@RefLastName,Mobile=@RefMobile,
      Email=@RefEmail,Address1=@RefAddress1,Address2=@RefAddress2,City=@RefCity,State=@RefState, ModifiedOn = 

GETDATE()
      WHERE EmployeeID = @EmployeeID
    END
      
  END
 

create procedure GetEmployee(
  @EmployeeID INT 
)
  AS
  BEGIN
      IF @EmployeeID IS NOT NULL
      BEGIN
        SELECT EMP.EmployeeID, EMP.FirstName,EMP.LastName,EMP.Mobile,EMP.Email,EMP.Address1,EMP.Address2,EMP.City,
        EMP.State, EMP.CreatedOn, EMP.ModifiedOn, 
        EMPREF.FirstName,EMPREF.LastName,EMPREF.Mobile,EMPREF.Email,EMPREF.Address1,EMPREF.Address2,EMPREF.City,
        EMPREF.State, EMPREF.CreatedOn, EMPREF.ModifiedOn
        FROM Employee EMP INNER JOIN EmployeeReference EMPREF ON
        EMP.EmployeeID = EMPREF.EmployeeID
        WHERE Emp.EmployeeID = @EmployeeID
      END
      ELSE IF @EmployeeID IS NULL
      BEGIN
        SELECT EMP.EmployeeID, EMP.FirstName,EMP.LastName,EMP.Mobile,EMP.Email,EMP.Address1,EMP.Address2,EMP.City,
        EMP.State, EMP.CreatedOn, EMP.ModifiedOn, 
        EMPREF.FirstName,EMPREF.LastName,EMPREF.Mobile,EMPREF.Email,EMPREF.Address1,EMPREF.Address2,EMPREF.City,
        EMPREF.State, EMPREF.CreatedOn, EMPREF.ModifiedOn
        FROM Employee EMP INNER JOIN EmployeeReference EMPREF ON
        EMP.EmployeeID = EMPREF.EmployeeID
      END
  END
  
 
 
 Create procedure PostJob(
  @Mode char(1),
  @PostedJobID int,
  @EmployerID int,
  @EmployeeDesignation varchar(50),
  @Description varchar(max),
  @Salary money,
  @Active bit
  )
  AS
  BEGIN
    IF @Mode = 'A'
      BEGIN
        INSERT INTO PostedJobs(EmployerID,EmployeeDesignation,Description,Salary,PostedDate,Active,CreatedOn)
        VALUES (@EmployerID,@EmployeeDesignation,@Description,@Salary,GETDATE(),1,CreatedOn)
      END
    ELSE IF @Mode = 'E'
      BEGIN
        UPDATE PostedJobs SET EmployeeDesignation=@EmployeeDesignation, Description=@Description,
        Salary =@Salary, PostedDate = @PostedDate, Active=@Active, ModifiedOn = GETDATE()
        WHERE PostedJobID = @PostedJobID
      END
    ELSE IF @Mode = 'D'
       BEGIN
         DELETE FROM PostedJobs WHERE PostedJobID = @PostedJobID
       END
  END

Create procedure PostJob(
  @Mode char(1),
  @PostedJobID int,
  @EmployerID int,
  @EmployeeDesignation varchar(50),
  @Description varchar(max),
  @Salary money,
  @Active bit
  )
  AS
  BEGIN
    IF @Mode = 'A'
      BEGIN
        INSERT INTO PostedJobs(EmployerID,EmployeeDesignation,Description,Salary,PostedDate,Active,CreatedOn)
        VALUES (@EmployerID,@EmployeeDesignation,@Description,@Salary,GETDATE(),1,GETDATE())
      END
    ELSE IF @Mode = 'E'
      BEGIN
        UPDATE PostedJobs SET EmployeeDesignation=@EmployeeDesignation, Description=@Description,
        Salary =@Salary, PostedDate = @PostedDate, ModifiedOn = GETDATE()
        WHERE PostedJobID = @PostedJobID
      END
    ELSE IF @Mode = 'D'
       BEGIN
         DELETE FROM PostedJobs WHERE PostedJobID = @PostedJobID
       END
  END
  
create table AppliedJobs(
  AppliedJobID int not null identity(1,1) primary key,
  PostedJobID int not null,
  EmployeeID int not null,
  EmployerID int not null,
  AppliedDate datetime,
  CreatedOn datetime,
  ModifiedOn datetime
  )
  
Create procedure ApplyJob(
  @Mode char(1),
  @PostedJobID int,
  @EmployerID int,
  @EmployeeID int
  )
  AS
  BEGIN
    IF @Mode = 'A'
      BEGIN
        INSERT INTO AppliedJobs(PostedJobID,EmployeeID,EmployerID,AppliedDate,CreatedOn)
        VALUES (@PostedJobID,@EmployeeID,@EmployerID,@AppliedDate,GETDATE())
      END
    ELSE IF @Mode = 'D'
       BEGIN
         DELETE FROM AppliedJobs WHERE AppliedJobID = @AppliedJobID
       END
  END
  

  
Create procedure GetAppliedJobs(
  @PostedJobID int,
  @EmployerID int,
  @EmployeeID int
  ) 
  AS
  BEGIN
    SELECT APP.AppliedJobID, APP.PostedJobID, APP.EmployeeID, APP.EmployerID, EMP.CompanyName APP.AppliedDate,
    POS.Description, POS.EmployeeDesignation, POS.PostedDate, POS.Salary 
    FROM PostedJobs POS INNER JOIN AppliedJobs APP ON POS.PostedJobID = APP.PostedJobID
    INNER JOIN Employer EMP ON EMP.EmployerID = APP.EmployerID
    WHERE APP.EmployeeID = @EmployeeID
    ORDER BY APP.AppliedDate DESC
  END


Create procedure GetPostedJobs(
  @PostedJobID int,
  @EmployerID int
  ) 
  AS
  BEGIN
    SELECT PostedJobID,EmployerID,EmployeeDesignation,Description,Salary,PostedDate,Active
    FROM PostedJobs WHERE EmployerID = @EmployerID
  END


