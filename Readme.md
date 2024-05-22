### MongoDB Task

## Design database for Zen class programme

create database zen_class;

use zen_class;

## Creating tables for the below:

1) # users: 

create table users(
    user_id int primary key,
    user_name varchar(20),
    email varchar(30),
    RoadMapSession_id int
);

2) # codekata:

create table codekata(
    codekata_id int primary key,
    user_id int,
    program_solved_today int,
    total_programs_solved int,
    foreign key (user_id) references users(user_id)
);

3) # attendance:

create table attendance (
    attendance_id int primary key,
    user_id int,
    date date,
    status varchar(10),
    foreign key (user_id) references users(user_id)
);

4) # topics:

create table topics (
    topic_id int primary key,
    title varchar(100),
    date date
);

5) # tasks:

create table tasks (
    task_id int primary key,
    title varchar(100),
    date date
);

create table task_submissions (
    task_id int,
    user_id int,
    primary key (task_id, user_id),
    foreign key (task_id)  references tasks(task_id),
    foreign key (user_id) references users(user_id)
);

6) # company_drives:

create table company_drives (
    id int primary key,
    company_name varchar(100),
    date date
);

create table drive_participants (
    drive_id int,
    user_id int,
    primary key (drive_id, user_id),
    foreign key (drive_id) references company_drives(drive_id),
    foreign key (user_id) references users(user_id)
);

7) # mentors: 

create table mentors (
    id int primary key,
    name varchar (100)
);

create table mentees (
    id int,
    user_id int,
    primary key(mentor_id, user_id),
    foreign key (mentor_id) references mentors(id),
    foriegn key (user_id) references users(user_id)
);

## Find all the topics and tasks which are thought in the month of October:

    select * from topics where date between '2023-10-01' and '2023-10-31';

    select * from tasks where date between '2023-10-01' and '2023-10-31';

## Find all the company drives which appeared between 15 oct-2020 and 31-oct-2020:

    select * from company_drives where date between '2020-10-15' and '2020-10-31';

## Find all the company drives and students who are appeared for the placement:

    select cd.company_name, cd.date, u.name as student_name from company_drives cd join drive_participants dp on cd.drive_id = dp.drive_id join users u on dp.user_id = u.user_id;

## Find the number of problems solved by the user in codekata:

    select user_id, SUM(total_problems_solved) as total_problems_solved from codekata group by user_id;

## Find all the mentors with who has the mentee's count more than 15:

    select m.name, count(*) AS mentee_count from mentors m join mentees me on m.id = me.mentor_id group by m.id having count(*) > 15;    

## Find the number of users who are absent and task is not submitted  between 15 oct-2020 and 31-oct-2020:

    select count(distinct a.user_id) as absent_and_not_submitted_count from attendance a left join task_submissions ts on a.user_id = ts.user_id and ts.task_id in ( select task_id FROM tasks where date between '2020-10-15' and '2020-10-31') where a.date between '2020-10-15' and '2020-10-31' and a.status = 'absent' and ts.user_id is null;
