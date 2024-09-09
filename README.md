# Análise de Dados de Crédito
**Meu primeiro projeto com SQL**
# **Olá! este é o meu projeto de Análise de Dados. Seja bem-vindo!**
Você também pode visualiza-lo pelo kaggle: https://www.kaggle.com/code/abreugabriel/analisando-dados-de-cr-dito

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
![consulta-dataset.PNG](https://github.com/user-attachments/assets/9a883d0d-3225-44f9-8482-81a9b8165bcd)

> Repare que há informações nulas na tabela (na), vamos analisar os valores de cada coluna?:


**Tipo e Identificação de cada dado:**

**Query:**
DESCRIBE credito

![describe.png](https://github.com/user-attachments/assets/a279fa7d-6bbb-404e-94a6-1ac2cb4112e7)


Agora que já sabemos, e entendemos qual o tipos de cada dado, vamos olhar mais atentamente para as strings.
**Quais são os tipos de escolaridade disponíveis no dataset?**

**Query:**
SELECT DISTINCT(escolaridade) FROM credito

![escolaridade.png](https://github.com/user-attachments/assets/e1ec7b9a-d71b-422d-9871-8601bd2bea83)


> Dataset conta com níveis diversos de escolaridade, e podemos perceber que temos valores nulos (na).


**Será que temos mais valores nulos?**

**Quais são os tipos de estado_civil disponíveis no dataset?**

**Query:**
SELECT DISTINCT(estado_civil) FROM credito

![estado_civil.png](https://github.com/user-attachments/assets/eb815fe4-2387-4336-b4c4-3ade53c55257)


> Sim! encontramos valores nulos nos dados de estado civil.


**Quais são os tipos de salario_anual disponíveis no dataset?**

**Query:** 
SELECT DISTINCT(salario_anual) FROM credito

![salarios.png](https://github.com/user-attachments/assets/49a63ac8-dcef-4512-bc93-98a1681f576b)

> Os salários presente no dataset não contém extamente o valor que o cliente ganha. Está informado a faixa salarial de cada um. E novamente, Também contém dados nulos!


**Quais são os tipos de cartões disponíveis no dataset?**

**Query:**
SELECT DISTINCT(tipo_cartao) FROM credito

![tipo_cartao.png](https://github.com/user-attachments/assets/d0a20dda-9167-439a-9d53-516c30ff649e)


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

![lim_cred.PNG](https://github.com/user-attachments/assets/cd94e949-f8c9-49b5-acb9-81c005b58663)

Retomando, para entender o que ocorre no dataset, vamos aplicar alguns questionamentos?:

**Nesse banco de dados, quantos clientes temos de cada escolaridade?**

**Query:**
SELECT COUNT(*) as qtd_formados, escolaridade FROM credit
GROUP BY escolaridade;

![qtd_escolaridade.png](attachment:7b5f5464-c5ac-415e-991f-fd6d69a340ac.png)

**Agora visando maior compreensão, colocarei meu conhecimento sobre o excel em prática. Com os resultados baixados em csv do Athena, vou aplicar a delimitação por texto e o localizar e substituir novamente, para assim gerar colunas de forma rápida e criar um gráfico para melhor visualização dos dados extraídos:**

![qtd_escolaridade](https://github.com/user-attachments/assets/8efef833-4f6b-4fc0-8faf-a6e58ebbc03e)


> Com isso obtemos que 46% dos clientes (destes 50) com maior crédito disponibilizados pela instituição possuem uma ótima progressão à vida acadêmica, 22% apresentam dados nulos, já 32% não possuem ensino superior. Uma ótima estratégia almejando esse perfil de clientes seria a oferta de nossos produtos com certificações de instituições acadêmicas, visando vender nossos produtos, serviços e impulsionar o perfil profissional destes clientes.


**Agora, qual a faixa salarial dos clientes com os 50 melhores limites de crédito?**

**Query:**
SELECT COUNT(*) as Qtd_Clientes, salario_anual FROM credit 
GROUP BY salario_anual

![g_CpSalario.PNG](https://github.com/user-attachments/assets/4fc2bc37-2225-44f0-b5c6-6436f914aaad)


> Mais de 70% de nossos clientes com os 50 maiores limites disponibilizados possuem salário superior a 80k, mas falhamos na coleta desta informação com 14% desses clientes, por estarem nulos. O que pode vir ser um grande problema, visto que é este dado é um parâmetro importânte para inadimplência, estudos de mercado, ou decisões de negócios baseadas em rendimento.


**Destes 50 maiores clientes com o produto de crédito, quantos clientes são homens e quantos são mulheres?**

**Query:**
SELECT COUNT(*), sex FROM credit
GROUP BY sex

![qtd_sex.png](https://github.com/user-attachments/assets/d8000350-86dc-4561-a962-dfddcf5c4929)


> Da tabela adquirida, visualizamos que dos maiores limites diponibilizados existem apenas 7 mulheres. Isto necessita de uma melhor investigação futura, agrupando aos dados de consumo de produtos, financeiro e acadêmico, para entendermos o real motivo dessa desigualdade.


**Queremos focar o nosso marketing de maneira adequada para nossos clientes, qual será a idade deles?**

**Query:**
SELECT avg(age) as media_idade, min(age) as min_idade, max(age) as max_idade, sex FROM credit
GROUP BY sex

![med_idade.PNG](https://github.com/user-attachments/assets/659edbe5-1c34-4741-b315-59b6bbddf2e6)


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
