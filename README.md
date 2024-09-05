# **Olá! este é o meu projeto de Análise de Dados. Seja bem-vindo!**
 Este é o meu primeiro projeto onde coloco em prática um pouco do que aprendi, onde avalio, exploro, inspeciono e manipulo dados, no qual selecionei um conjunto de dados fictício que contém informações sobre clientes de uma instituição bancária.
     Para que fique a par deste projeto, irei te informar os dados presentes no Dataset:
     
 ## Vamos explorar os Dados?:
* age = idade do cliente;
* sex = sexo do cliente (F ou M);
* dependentes = número de dependentes do cliente;
* escolaridade = nível de escolaridade do clientes;
* estado_civil = situação civil;
* salario_anual = faixa salarial do cliente;
* tipo_cartao = tipo de cartao do cliente;
* qtd_produtos = quantidade de produtos que o cliente possui;
* iteracoes_12m = quantidade de iterações/transações no último ano;
* meses_inativo_12m = quantidade de meses que o cliente ficou inativo
* limite_credito = limite de crédito do cliente;
* valor_transacoes_12m = valor das transações do último ano;
* qtd_transacoes_12m  = quantidade de transações do último ano.

As análises e explorações consistiu no uso do AWS Athena, em conjunto com o S3 Bucket para armazenamento do dataset, que foi disponibilizados em: https://github.com/andre-marcos-perez/ebac-course-utils/tree/main/dataset

## **Explorando e Avaliando os dados:**

Após criar uma tabela e determinar cada tipo de dado, vamos a exploração dos dados:

**Primeiro apliquei a query abaixo para saber a quantidade de informações que há em nossa base de dados:**

**Query:**
SELECT COUNT(*), FROM credito;
> Resultado: 2564 linhas


**OBS:** O dataset do link informado anteriormente contém mais linhas do que a seleção utilizada. Mas iremos reduzi-lo novamente e relatarei conforme evoluímos durante a leitura deste projeto.

**Visualize os dados:** 

**Query:**
SELECT * FROM credito LIMIT 10;
![consulta-dataset.PNG](attachment:d2311748-a249-48a4-9e8a-2e821b566670.png)

> Repare que há informações nulas na tabela (na), vamos analisar os valores de cada coluna?:


**Tipo e Identificação de cada dado:**

**Query:**
DESCRIBE credito

![describe.png](attachment:5d3c4aa1-1831-4d29-88d2-de9733efe75a.png)


Agora que já sabemos, e entendemos qual o tipos de cada dado, vamos olhar mais atentamente para as strings.
**Quais são os tipos de escolaridade disponíveis no dataset?**

**Query:**
SELECT DISTINCT(escolaridade) FROM credito

![escolaridade.png](attachment:269fcc50-a0f2-4628-8992-472872ac772a.png)

> Dataset conta com níveis diversos de escolaridade, e podemos perceber que temos valores nulos (na).


**Será que temos mais valores nulos?**

**Quais são os tipos de estado_civil disponíveis no dataset?**

**Query:**
SELECT DISTINCT(estado_civil) FROM credito

![estado_civil.png](attachment:a74b6cf2-4416-41eb-bb5d-2d1929c93b9b.png)

> Sim! encontramos valores nulos nos dados de estado civil.


**Quais são os tipos de salario_anual disponíveis no dataset?**

**Query:** 
SELECT DISTINCT(salario_anual) FROM credito

![salarios.png](attachment:00357bb5-227e-44ee-b8c0-a4749bccd694.png)

> Os salários presente no dataset não contém extamente o valor que o cliente ganha. Está informado a faixa salarial de cada um. E novamente, Também contém dados nulos!


**Quais são os tipos de cartões disponíveis no dataset?**

**Query:**
SELECT DISTINCT(tipo_cartao) FROM credito

![tipo_cartao.png](attachment:88c82949-a349-4beb-b69b-cdf3f1f95214.png)

> Aqui vemos que não existem valores nulos.

