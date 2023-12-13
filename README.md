# Batch-Input-in-ABAP
Em ABAP (Advanced Business Application Programming), a criação de entradas em lote (batch input) é geralmente feita usando a função `BDC_OPEN_GROUP` para abrir um grupo de processamento em lote, seguida por várias chamadas para `BDC_INSERT` para adicionar transações ao grupo, e finalmente, `BDC_CLOSE_GROUP` para fechar o grupo e iniciar o processamento em lote.

Aqui está um exemplo simples de como você pode criar uma entrada em lote em ABAP:

```abap
REPORT ZBATCH_INPUT_EXAMPLE.

DATA: v_program TYPE sy-repid,
      v_dynpro TYPE sy-dynnr,
      v_dynbegin TYPE sy-dynnr,
      v_dynend TYPE sy-dynnr.

DATA: BEGIN OF gt_bdcdata OCCURS 0,
        bdcdata(255) TYPE C,
      END OF gt_bdcdata.

DATA: v_bdcmsgcoll TYPE TABLE OF bdcmsgcoll,
      v_bdcmsg TYPE bdcmsg,
      v_bdcmsgtxt TYPE bdcmsgtxt.

* Função para adicionar uma transação ao grupo de processamento em lote
FORM add_transaction USING p_program TYPE sy-repid
                         p_dynpro TYPE sy-dynnr
                         p_dynbegin TYPE sy-dynnr
                         p_dynend TYPE sy-dynnr.

  CLEAR: v_bdcmsgcoll, gt_bdcdata[].

* Populando o grupo de dados BDC
  APPEND 'BDC_OPEN_GROUP' TO gt_bdcdata.
  APPEND 'GROUP' TO gt_bdcdata.
  APPEND 'BDCGRP1' TO gt_bdcdata.

  APPEND 'BDC_INSERT' TO gt_bdcdata.
  APPEND '0' TO gt_bdcdata.
  APPEND 'TRANSACTION' TO gt_bdcdata.
  APPEND p_program TO gt_bdcdata.

  APPEND 'BDC_INSERT' TO gt_bdcdata.
  APPEND '1' TO gt_bdcdata.
  APPEND 'DYNPRO' TO gt_bdcdata.
  APPEND p_dynpro TO gt_bdcdata.
  APPEND 'DYNBEGIN' TO gt_bdcdata.
  APPEND p_dynbegin TO gt_bdcdata.
  APPEND 'DYNNR' TO gt_bdcdata.
  APPEND p_dynend TO gt_bdcdata.

  APPEND 'BDC_CLOSE_GROUP' TO gt_bdcdata.

* Executando a transação em lote
  CALL TRANSACTION 'SM35' USING gt_bdcdata
                                MODE 'E'
                                UPDATE 'S'
                                MESSAGES INTO v_bdcmsgcoll.

* Verificando mensagens de retorno
  LOOP AT v_bdcmsgcoll INTO v_bdcmsg.
    LOOP AT v_bdcmsg-msgtxt INTO v_bdcmsgtxt.
      WRITE: / 'Mensagem:', v_bdcmsgtxt-msgid,
             / 'Número:', v_bdcmsgtxt-msgno,
             / 'Texto:', v_bdcmsgtxt-msgty,
             / v_bdcmsgtxt-msgid.
    ENDLOOP.
  ENDLOOP.

ENDFORM.

* Exemplo de chamada da função para adicionar uma transação
START-OF-SELECTION.

  v_program = 'SAPLSMTR_NAVIGATION'.
  v_dynpro = '1000'.
  v_dynbegin = '1000'.
  v_dynend = '1000'.

  PERFORM add_transaction USING v_program v_dynpro v_dynbegin v_dynend.

```

Este é um exemplo muito básico e genérico. Certifique-se de adaptá-lo às suas necessidades específicas, incluindo a modificação do programa (`v_program`), a tela inicial (`v_dynpro`), a tela inicial de intervalo (`v_dynbegin`) e a tela final de intervalo (`v_dynend`). Além disso, você pode precisar ajustar o nome do grupo (`BDCGRP1`) conforme necessário.

Tenha em mente que a criação de entradas em lote em ABAP é uma técnica poderosa, mas pode exigir um entendimento profundo do sistema SAP e das transações específicas que você está tentando automatizar. Certifique-se de testar cuidadosamente em um ambiente de desenvolvimento antes de usar em um sistema de produção.
