# Data Warehouse - Implementação

## Cenário e objetivos

  O cenário desenvolvido neste projeto visa simular a implementação de um Data Warehouse para uma empresa de manufatura que busca gerar relatórios para melhor compreensão dos seus processos de negócio.
  Possuindo diversos dados sobre suas fabricas, seus fornecedores e vendedores, a empresa busca utiliza-los para geração dos seguintes relatórios:
 
- **Vendas**: Relatórios que fornecem informações sobre as vendas por região, produto, canal de vendas e período de tempo, permitindo a empresa identificar os produtos mais vendidos, as tendências de mercado e os canais de vendas mais eficazes.
  
- **Estoque**:  Relatórios que mostram informações sobre os níveis de estoque de matéria-prima, produtos em processo e produtos acabados, permitindo que a empresa gerencie seu estoque de forma mais eficiente e reduza os custos de armazenamento.
  
- **Produção**: Relatórios que mostram informações sobre a eficiência da produção, incluindo o tempo e ciclo, taxa de defeito, utilização de máquinas e outros indicadores de desempenho, permitindo que a empresa identifique áreas de melhoria e aumente a eficiência da produção.
  
- **Manutenção**: Relatórios que fornecem informações sobre as atividades de manutenção da empresa, incluindo o tempo de inatividade, manutenção preventiva e corretiva, custos de manutenção e outros indicadores de desempenho, permitindo que a empresa gerencie seus ativos de forma mais eficiente.
  
- **Financeiros**: Relatórios que mostram informações financeiras, como receita, despesas, margem de lucro, fluxo de caixa e outras métricas financeiras importantes, permitindo que a empresa avalie sua saúde financeira e tome decisões informadas.
  
- **Qualidade**: Relatórios que fornecem informações sobre a qualidade dos produtos, incluindo os dados de inspeção e teste, o índice de defeito e outros indicadores de qualidade, permitindo que a empresa identifique áreas de melhoria na qualidade e tome medidas corretivas.

## Solução proposta

  A solução implementada utiliza das ferramentas abaixo relacionadas, disposta de forma a estruturar a seguinte arquitetura: 

![ZoomOut](https://github.com/JvQueiros/Implementacao_DW/assets/95942380/d1adbc2d-b6cb-484b-8b64-7f590a592580)

  Depreende-se da imagem apresentada que, como fonte, utilizou-se um banco de dados PostgreSQL hospedado em um servidor dedicado da empresa; e um segundo servidor foi utilizado para hospedar o Docker, possibilitando o deploy de dois containers, o primeiro rodando o Airbyte – responsável pela extração dos dados da fonte – e o segundo rodando, também, uma instância do PostgresSQL.

  Neste segundo banco de dados, foi criado dois schemas, sendo um a Staging Area e o outro o próprio Data Warehouse, veja-se:

![ZoomIn](https://github.com/JvQueiros/Implementacao_DW/assets/95942380/3522860f-a91d-4994-952f-aa1e9a2425b4)

  Dessarte, ao final chegamos a seguinte estrutura:

![PgAdmin](https://github.com/JvQueiros/Implementacao_DW/assets/95942380/2a81256d-21ae-4cfa-9e02-e37d62841e76) 
![All_Proc](https://github.com/JvQueiros/Implementacao_DW/assets/95942380/665e66e1-dc30-4501-b484-29bbec236d28)

  Com o Airbyte, os dados foram transferidos para a Staging Area em sua forma bruta, sem filtro ou transformação; o objetivo é gerar a menor sobrecarga possível no servidor de origem. Uma vez na Staging Area, foram limpos, transformados e carregados no Data Warehouse com linguagem SQL. Ou seja, o processo ETL foi feito em duas etapas assíncronas. 

![Airbyte](https://github.com/user-attachments/assets/4cc294dc-876c-4121-9e0d-c4c6c574253f))

  A utilização de query’s SQL para tratamento e carga mostrou-se vantajosa na segunda etapa pois torna o processo mais veloz e eficiente, tendo que a Staging Area e o Data Warehouse estão no mesmo servidor e compartilham o mesmo banco de dados. Essa arquitetura da Staging Area ficar fora do banco de dados de origem e compartilhar o banco de dados de destino é chamada de “Staging Remoto” e sua principal vantagem é que acelera o processo de carga.

　➡　Vantagens da arquitetura proposta:

- Evita-se sobrecarga de processamento no servidor da fonte de dados.

- O ETL é assíncrono, o que significa que as etapas podem ser executadas em momentos diferentes do dia.

- A Staging Area centraliza as tarefas de limpeza, transformação e organização de dados simplificando o processo e permitindo auditoria.

　➡　Desvantagens:

- Pode haver sobrecarga na performance do DW dependendo do horário em que as transformações forem realizadas na Staging Area.

- Pode haver um gap entre a extração de dados da fonte e carga na Staging Area para processamento. 
	Por exemplo, imagine que a extração dos dados ocorra todo dia às 19h e os dados só comecem a ser processados na Staging Area às 20h. Essa diferença de 1h é importante para os relatórios? Pois, os dados gerados nesse interim só serão extraídos no dia seguinte. 

No cenário proposto, o Docker serviu bem pela facilidade de implementação e deploy de “servidores isolados”, por essa razão, muitos ambientes corporativos também o utilizam. Para volumes de dados na ordem de milhões de registros não há problema, entretanto, para volumes na ordem de bilhões de registros, outras tecnologias devem ser consideradas. 

## Conclusão

O maior desafio de se trabalhar com a implementação de um Data Warehouse é conseguir manter a mente aberta para tentarmos cobrir toda a necessidade de negócio do usuário final. Através desse projeto foi possível praticar e implementar conceitos importantes que nos guiam a exercitar nossa capacidade de reflexão nos relatórios que serão utilizados, no nível de granularidade dos dados, na sobrecarga dos sistemas entre outros aspectos.

Como uma possível melhoria, podemos pensar em isolar a Staging Area em outro servidor, utilizar linguagem Python para fazer extração da fonte, automatizando com o Airflow, por exemplo.

## Code 📃
Códigos e scripts utilizados: [scripts](./scripts_files)

