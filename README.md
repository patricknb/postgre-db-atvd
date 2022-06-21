# postgre-db-atvd

definir tabela

create table Ambulatorios (nroa int, andar numeric(2) not null, capacidade smallint, primary key(nroa));

create table Medicos (codm int, nome varchar(40) not null, idade smallint not null, cidade varchar(40), CPF numeric(11) not null unique, especialidade varchar(30), nroa int, primary key(codm), foreign key(nroa) references Ambulatorios);

create table Pacientes (codp int, nome varchar(40) not null, idade smallint not null, cidade varchar(40), CPF numeric(11) not null unique, doenca varchar(40) not null, primary key(codp));

create table Funcionarios (codf int, nome varchar(40) not null, idade smallint not null, cidade varchar(40), CPF numeric(11) not null unique, salario numeric(10), primary key(codf));

create table Consultas (codm int, codp int, amd date, hora time, primary key(codm, amd, hora), foreign key(codm) references Medicos, foreign key(codp) references Pacientes);



insert into Ambulatorios values (1,1,30), (2,1,50), (3,2,40), (4,2,25), (5,2,55);

insert into Medicos (codm, nome, idade, especialidade, CPF, cidade, nroa) values (1, 'Joao', 40, 'ortopedia', 10000100000, 'Florianopolis', 1), (2, 'Maria', 42, 'traumatologia', 10000110000, 'Blumenau', 2), (3, 'Pedro', 51, 'pediatria', 11000100000, 'Sao Jose', 2), (4, 'Carlos', 28, 'ortopedia', 11000110000, 'Joinville', NULL), (5, 'Marcia', 33, 'neurologia', 11000111000, 'Biguacu', 3);

insert into Pacientes (codp, nome, idade, cidade, CPF, doenca) values (1,'Ana', 21, 'Florianopolis', 20000200000, 'gripe'), (2,'Paulo', 24, 'Palhoca', 20000220000, 'fratura'), (3,'Lucia', 30, 'Biguacu', 22000200000, 'tendinite'), (4,'Carlos', 28, 'Joinville',11000110000, 'sarampo');

insert into Funcionarios (codf, nome, idade, CPF, cidade, salario) values (1,'Rita', 32, 20000100000, 'Sao Jose', 1200), (2,'Vera', 55, 30000110000, 'Palhoca', 1220), (3,'Caio', 45, 41000100000, 'Florianopolis', 1100), (4,'Carlos', 44, 51000111000, 'Florianopolis', 2500), (5,'Paula', 33, 61000111000, 'Florianopolis', 2500);

insert into Consultas (codm, codp, amd, hora) values 
(1,1,'2020/10/12','14:00'),
(1,4,'2020/11/13','10:00'),
(2,1,'2020/10/13','9:00'),
(2,2,'2020/10/13','11:00'),
(2,3,'2020/10/14','14:00'),
(2,4,'2020/10/14','17:00'),
(3,1,'2020/10/19','18:00'),
(3,3,'2020/10/12','10:00'),
(3,4,'2020/10/19','14:30'),
(4,4,'2020/10/20','13:00'),
(4,4,'2020/10/22','19:30');

atualizações

UPDATE pacientes SET cidade = 'ilhota' where nome = 'Paulo';

UPDATE consultas SEt amd = '2020-11-4', hora = '12:00:00' WHERE codm = 1 AND codp = 4;

UPDATE consultas SET hora = '14:30:00' WHERE codm = 3 and codp = 4;

DELETE FROM funcionarios WHERE codf = 4;

DELETE FROM consultas WHERE hora > '19:00:00';

DELETE FROM medicos WHERE cidade = 'Biguacu' or cidade = 'Palhoca';

consultas

SELECT * FROM medicos WHERE idade < 40 or especialidade != 'traumatologia';

SELECT nome, idade FROM pacientes where cidade != 'Florianopolis';

SELECT nome, idade*12 as idade_mes FROM pacientes;

SELECT max(hora) FROM consultas WHERE amd = '2020-10-13';

SELECT AVG(idade) as media_idade, COUNT(nroa) as ambulatorios_atendidos FROM medicos;

SELECT codf, nome, (salario - (salario*0.2)) as salario_80 FROM funcionarios;

SELECT * from funcionarios WHERE nome LIKE '%a';

SELECT nome, especialidade FROM medicos WHERE nome LIKE '_o%o';

SELECT * FROM pacientes WHERE doenca in ('tendinite', 'fratura', 'gripe', 'sarampo');

SELECT nome, idade, cpf FROM medicos WHERE cidade = 'Florianopolis'
UNION
SELECT nome, idade, cpf FROM pacientes WHERE cidade = 'Florianopolis'
UNION
SELECT nome, idade, cpf from funcionarios WHERE cidade = 'Florianopolis'

junção

SELECT nome, cpf FROM medicos NATURAL JOIN pacientes;

SELECT funcionarios.nome, medicos.nome from funcionarios JOIN medicos on funcionarios.cidade = medicos.cidade;

SELECT codp, nome from pacientes NATURAL JOIN consultas WHERE hora > '14:00:00';

SELECT nroa, andar FROM ambulatorios NATURAL JOIN medicos WHERE medicos.especialidade = 'ortopedista'

SELECT codm, nome FROM medicos NATURAL JOIN ambulatorios WHERE nroa = 2 and nome != 'Pedro';

SELECT nome, idade FROM medicos NATURAL JOIN consultas WHERE codp = 1;

SELECT nome, salario FROM funcionarios WHERE cidade = 'Florianopolis' and salario > 1100;

SELECT ambulatorios.nroa, andar, capacidade, codm, nome FROM ambulatorios LEFT JOIN medicos on ambulatorios.nroa = medicos.nroa

SELECT medicos.cpf as medico_cpf, medicos.nome as medico_nome, foo.cpf as paciente_cpf, foo.nome as paciente_nome, foo.amd as data_marcada FROM medicos LEFT JOIN (SELECT cpf, nome, amd, codm FROM pacientes NATURAL JOIN consultas) as foo on medicos.codm = foo.codm
