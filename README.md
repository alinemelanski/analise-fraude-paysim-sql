# Análise de Fraudes com SQL — PaySim

Investigação de padrões de fraude em transações financeiras usando SQL. O objetivo foi responder perguntas de negócio que um analista de prevenção a fraudes enfrenta no dia a dia: onde o fraudador age, como ele movimenta o dinheiro e se as regras de detecção existentes funcionam.

## Dados

Dataset sintético **PaySim**, disponível no Kaggle: [Synthetic Financial Datasets For Fraud Detection](https://www.kaggle.com/datasets/ealaxi/paysim1).

São cerca de **6,3 milhões de transações** simuladas de um serviço de dinheiro móvel, com **8.213 fraudes** marcadas. Cada linha traz o tipo da transação, o valor, os saldos antes e depois e se ela foi fraude. O tempo é medido em `step`, em que cada unidade equivale a 1 hora, ao longo de 30 dias.

> O arquivo de dados não está neste repositório por causa do tamanho. Para reproduzir, baixe o CSV no link acima e importe como a tabela `transacoes`.

## Ferramentas

- SQLite
- DB Browser for SQLite

## Principais descobertas

- **As fraudes acontecem só em dois tipos de transação:** `TRANSFER` e `CASH_OUT`.
- **O fraudador esvazia a conta da vítima:** em 8.008 transações o saldo inteiro foi levado, e todas eram fraude. Esse padrão cobre cerca de **97,5% das fraudes** da base.
- **A regra de detecção existente quase não protege:** a regra `isFlaggedFraud` pegou só **16 das 8.213 fraudes (0,2%)**. Uma regra baseada em saldo esvaziado pegou **8.008 (97,5%)**, sem travar nenhum cliente legítimo nesta base.
- **O dinheiro é pulverizado:** as contas que mais recebem de pessoas diferentes receberam no máximo 2 fraudes cada. Volume de remetentes, sozinho, não identifica conta laranja.
- **O saque é imediato:** em cerca de **99% das transferências fraudulentas**, um saque de mesmo valor aconteceu na mesma hora. O fraudador tira o dinheiro do sistema antes que haja tempo de bloquear.

## Recomendações

- Substituir a regra atual pela regra de saldo esvaziado, acionando **autenticação reforçada** (biometria) em vez de bloqueio automático, para barrar o fraudador sem travar o cliente legítimo.
- Combinar sinais em alertas: acesso de dispositivo novo, valor acima do padrão do cliente, saldo esvaziado e transferência seguida de saque em pouco tempo.
- Avaliar toda regra nova com uma **matriz de confusão** antes de colocá-la em produção.

> Como a base é simulada, os resultados precisam ser validados em dados reais. Na operação, é provável que alguns clientes legítimos também esvaziem a conta, gerando falsos positivos.

## O que pratiquei

- Agregações: `GROUP BY`, `COUNT`, `SUM`, `COUNT(DISTINCT)`, `HAVING`
- Filtros combinados com `AND`, `NOT`, `IN` e `LIKE`
- Lógica condicional com `CASE WHEN`
- Matriz de confusão (falso positivo e falso negativo), precisão e cobertura
- Comparação de regras com `UNION ALL`
- Funções de janela (`LEAD` com `PARTITION BY`) e subconsultas

## Estrutura

| Arquivo | Conteúdo |
|---|---|
| `consultas_fraude.sql` | Todas as consultas, cada uma com a pergunta de negócio e a conclusão |

## Próximos passos

- Dashboard em **Power BI** com os indicadores de fraude
- Análise e simulação de regras em **Python** (pandas)

## Autora

**Aline Melanski**
[LinkedIn](https://www.linkedin.com/in/aline-melanski-840a65113) · [GitHub](https://github.com/alinemelanski)