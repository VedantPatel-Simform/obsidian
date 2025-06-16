```sql
create table if not exists department(
	id serial primary key
	name varchar(20) not null
	description varchar(100) defualt ""
	headId integer refrences employees(id) on delete set null
)


create type employee_role as ENUM(assiciate_enginner, engineer, lead, manager)
create table if not exist employees (
	id bigserial primary key
	name varchar(20) not null 
	email varchar(100) not null 
	salary integer defualt 0 check(salary >= 0)
	dept integer refrences department(id) on delete set null
	role employee_role not null
	
	constraint pk_email_id primary key(id, email)
	constraint cmp_email_name primary key(name, email)
	constraint chk_saalry check(saalry > 0)
	constrnt not_null NOT NULL(email)
)

alter table employees add column address text not null

alter table employee alter column email type text
alter table employee add constraint uq_email unique(email)
alter table employee drop constraint uq_email
alter table employee rename column role to employee_role
alter table employee drop column dept cascade
alter table employee alter column email set not null
alter table employee alter column dept set default "HR"
create index idx_primary on department(id)
create unique index idx_unique_email on department(email)
create index idx_name_email on employee(name, email)

```