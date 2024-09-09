# Análise de Dados de Crédito
**Meu primeiro projeto com SQL**
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
* 
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

## **Análise de dados**

Após explorarmos os dados e entendermos as informações que possuímos em nosso banco de dados, vamos efetuar uma alteração. Mesmo sabendo que quanto maior nossa densidade de dados, maior acertividade, iremos particionar os dados, já que o Athena aplica o método de cobrança: processamento x custo. Assim também otimizamos nossas análises e manipulação de dados. 
Portanto, visando nossos clientes com os 50 maiores limites créditos vamos aplicar esta requisição:

**Query:**
SELECT * FROM credito
ORDER BY limite_credito DESC
LIMIT 50;

Após essa consulta, baixei os resultados e criei uma nova tabela, mas encontrei alguns problemas:
- Alguns dados ficaram em branco;
- Parte da primeira linha estava com a nomeação do dado.
Após visualizar pelo excel identifiquei a presença do caracter aspas em todos os dados, e na primeira linha havia a nomenclatura de cada dado. Para correção apliquei o comando de localizar e substituir; para todas as aspas por caracteres vazios no excel. Após correção criei uma nova tabela no Athena chamada **credit**.

![lim_cred.PNG](attachment:33cfc53d-fb72-4510-a8d2-c15a54251ded.png)

Retomando, para entender o que ocorre no dataset, vamos aplicar alguns questionamentos?:

**Nesse banco de dados, quantos clientes temos de cada escolaridade?**

**Query:**
SELECT COUNT(*) as qtd_formados, escolaridade FROM credit
GROUP BY escolaridade;

![qtd_escolaridade.png](attachment:7b5f5464-c5ac-415e-991f-fd6d69a340ac.png)

**Agora visando maior compreensão, colocarei meu conhecimento sobre o excel em prática. Com os resultados baixados em csv do Athena, vou aplicar a delimitação por texto e o localizar e substituir novamente, para assim gerar colunas de forma rápida e criar um gráfico para melhor visualização dos dados extraídos:**

![qtd_escolaridade](attachment:2e797070-cea2-4c8a-b712-ac68fd7c4f64.png)

> Com isso obtemos que 46% dos clientes (destes 50) com maior crédito disponibilizados pela instituição possuem uma ótima progressão à vida acadêmica, 22% apresentam dados nulos, já 32% não possuem ensino superior. Uma ótima estratégia almejando esse perfil de clientes seria a oferta de nossos produtos com certificações de instituições acadêmicas, visando vender nossos produtos, serviços e impulsionar o perfil profissional destes clientes.


**Agora, qual a faixa salarial dos clientes com os 50 melhores limites de crédito?**

**Query:**
SELECT COUNT(*) as Qtd_Clientes, salario_anual FROM credit 
GROUP BY salario_anual

![g_CpSalario.PNG](attachment:b138bc07-a770-40ea-b5dd-8cfc3dd0df64.png)

> Mais de 70% de nossos clientes com os 50 maiores limites disponibilizados possuem salário superior a 80k, mas falhamos na coleta desta informação com 14% desses clientes, por estarem nulos. O que pode vir ser um grande problema, visto que é este dado é um parâmetro importânte para inadimplência, estudos de mercado, ou decisões de negócios baseadas em rendimento.


**Destes 50 maiores clientes com o produto de crédito, quantos clientes são homens e quantos são mulheres?**

**Query:**
SELECT COUNT(*), sex FROM credit
GROUP BY sex

![qtd_sex.png](attachment:7493328d-f11f-4c1d-bbe0-1e598d9e83a8.png)

> Da tabela adquirida, visualizamos que dos maiores limites diponibilizados existem apenas 7 mulheres. Isto necessita de uma melhor investigação futura, agrupando aos dados de consumo de produtos, financeiro e acadêmico, para entendermos o real motivo dessa desigualdade.


**Queremos focar o nosso marketing de maneira adequada para nossos clientes, qual será a idade deles?**

**Query:**
SELECT avg(age) as media_idade, min(age) as min_idade, max(age) as max_idade, sex FROM credit
GROUP BY sex

![med_idade.PNG](attachment:d909ea70-5764-47d7-9c61-c03192695e40.png)

> Não foi possível obter informações relevantes com a idades mínima, já que são similares. Já a média há uma diferença e a máx é desproporcional, com esta query podemos expandir a análise aos padrões de consumo, e densenvolver novos produtos, ou até mesmo direcionar aos clientes que estão fora deste ranking "TOP50 Limite de Crédito" os serviçoes e produtos mais consumidos.


Por fim, desejo ampliar meu conhecimentos sobre a sintaxe, para gerar análises mais complexas e amplia-las aos padrões de consumo.

# Conclusão

Portanto, das análises obtidas do dataset de crédito, alguns **Insights** desse "TOP50 Limite de Crédito" interessantes são:

- Do "TOP50 Limite de Crédito", apenas 7 são de mulhres, havendo uma grande desigualdade;
- Dos clientes desse "TOP50 Limites de Crédito", aproximadamente 75% possuem renda superior a 80k;
- A maior parte dos clientes do banco é masculino;
- A escolaridade não influenciou no limite disponibilizado, visto que 32% dos clientes não possuem ensino superior;
- A idade influência no limite de crédito, visto que a idade média para adentrar nesse ranking é 45 anos para os homens, e 38 anos para as mulheres.

**Ao fim destes projeto desejo explorar melhor os dados, para entender: o motivo da baixa quantidade de mulheres neste dataset.
Além de Analisar: Se o consumo de produtos e o tipo de cartão influênciam no limite disponibilizado, se a quantidade de dependentes é um impacto para adentrar nesta lista+**
