## Batch-Input
O termo "batch input" é frequentemente associado ao método de gravação e reprodução de transações em lote usando transações de gravação. Vamos abordar isso de forma mais específica.

O Batch Input é um método no SAP que permite a gravação e reprodução de transações em lote. A ideia é gravar uma transação manualmente e, em seguida, reproduzi-la várias vezes usando os dados gravados. Aqui está um exemplo de como você pode criar e executar um Batch Input:

1. **Gravação de uma Transação:**
   - Execute a transação manualmente usando a transação `SHDB`. 
   - Grave as ações executadas durante a transação.
   - Ao finalizar, forneça um nome para o programa de Batch Input.

2. **Criação de um Programa de Batch Input:**
   - Vá para a transação `SE38` para criar um novo programa ABAP.
   - No código do programa, você precisará chamar a função `BDC_OPEN_GROUP`, adicionar os dados gravados usando `BDC_INSERT` e, finalmente, fechar o grupo usando `BDC_CLOSE_GROUP`.

   Exemplo de código ABAP:

   ```abap
   REPORT ZBATCH_INPUT_EXAMPLE.

   DATA: v_bdcdata TYPE TABLE OF bdcdata,
         v_dynpro TYPE sy-dynnr.

   " Início do grupo de Batch Input
   CALL FUNCTION 'BDC_OPEN_GROUP'
     EXPORTING
       group = 'BDC_GROUP'
       USER = sy-uname
       CLIENT = sy-mandt.

   " Adicionar dados gravados
   APPEND VALUE #( 'BDC_INSERT' '0' 'TRANSACTION' 'TransactionCode' ) TO v_bdcdata.

   " Fim do grupo de Batch Input
   CALL FUNCTION 'BDC_CLOSE_GROUP'.

   " Executar o Batch Input
   CALL TRANSACTION 'TransactionCode' USING v_bdcdata.

   ```

3. **Execução do Programa:**
   - Execute o programa que você criou usando a transação `SE38`.
   - Isso executará a transação em lote usando os dados gravados.

Lembre-se de substituir 'TransactionCode' pelo código da transação que você gravou.
