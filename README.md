# Classificação de Clientes por Histórico de Compras

## Visão Geral

Este repositório fornece uma solução para o problema de classificação de clientes em uma empresa de comércio eletrônico, com base em seus históricos de compras. A classificação é determinada pelo valor total gasto por cada cliente. Para tornar o processo eficiente, a solução proposta utiliza funções, uma view e uma procedure.

## Estrutura do Código

### Função para Calcular Valor Total Gasto por Cliente

A função `fn_calcula_valor_gasto_cliente` calcula o valor total gasto por um cliente com base no histórico de compras. Ela aceita o parâmetro `@clienteID` (identificador do cliente) e retorna o valor total gasto como um decimal.

```sql
CREATE FUNCTION fn_calcula_valor_gasto_cliente(@clienteID INT)
RETURNS DECIMAL(10, 2)
AS
BEGIN
    DECLARE @total DECIMAL(10, 2);
    SELECT @total = SUM(Valor)
    FROM Compras
    WHERE ClienteID = @clienteID;
    RETURN ISNULL(@total, 0);
END;
```

### View para Exibir Dados de Clientes Classificados
A view `vw_classificacao_clientes` é criada para exibir os dados dos clientes juntamente com o valor total gasto. Esta view utiliza a função `fn_calcula_valor_gasto_cliente` para calcular o valor total.

```sql
CREATE VIEW vw_classificacao_clientes
AS
SELECT
    ClienteID,
    Nome,
    Email,
    dbo.fn_calcula_valor_gasto_cliente(ClienteID) AS ValorTotalGasto
FROM
    Clientes;
```

### Procedure para Atualizar Classificação dos Clientes
A procedure `sp_atualizar_classificacao_clientes` é responsável por atualizar a classificação dos clientes com base nos valores totais gastos. A classificação é feita atribuindo rótulos como `"Cliente VIP"`, `"Cliente Premium"` e `"Cliente Regular"`.

```sql
CREATE PROCEDURE sp_atualizar_classificacao_clientes
AS
BEGIN
    UPDATE Clientes
    SET Classificacao = 
        CASE 
            WHEN dbo.fn_calcula_valor_gasto_cliente(ClienteID) > 1000 THEN 'Cliente VIP'
            WHEN dbo.fn_calcula_valor_gasto_cliente(ClienteID) > 500 THEN 'Cliente Premium'
            ELSE 'Cliente Regular'
        END;
END;
```

### Resumo:
* A função `fn_calcula_valor_gasto_cliente` calcula o valor total gasto por um cliente com base no histórico de compras.
* A view `vw_classificacao_clientes` exibe os dados dos clientes juntamente com o valor total gasto, usando a função criada.
* A procedure `sp_atualizar_classificacao_clientes` atualiza a classificação dos clientes com base nos valores totais gastos, atribuindo rótulos como `"Cliente VIP"´, `"Cliente Premium"` e `"Cliente Regular"`.
