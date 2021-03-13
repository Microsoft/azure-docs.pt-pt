---
title: Módulo de função ABAP de extração de metadados em SAP R3 - Azure Purview
description: Este artigo descreve os passos para implantar o módulo de função ABAP no SERVIDOR SAP
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 9bd3c315fcc15317a9fa483289fdc326ca6aa47f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614365"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>Implementar o módulo de função ABAP de extração de metadados para a família de pontes SAP R3

Este artigo descreve os passos para implantar o módulo de função ABAP no SERVIDOR SAP.

## <a name="overview"></a>Descrição Geral

A sap business suite 4 HANA (S/4HANA), ECC e r/3 ERP bridge podem ser usadas para extrair metadados do Servidor SAP. Isto é conseguido colocando o módulo de função ABAP no servidor SAP. Este módulo de função é remotamente acessível pela ponte para consultar e transferir (como ficheiro de texto) os metadados que contêm dentro do servidor SAP.

Quando executado, a ponte também:

1. Importa metadados de um ficheiro já descarregado localmente de uma execução de ponte anterior.

2. Invoca o módulo ABAP API, aguarda o download e, em seguida, importa metadados desse ficheiro.

Este documento detalha os passos necessários para a implementação deste módulo.

> [!Note]
> Foram elaboradas as seguintes instruções com base no SAP GUI v.7.2

## <a name="deployment-of-the-module"></a>Implantação do Módulo

### <a name="create-a-package"></a>Criar um Pacote

Este passo é opcional, e um pacote existente pode ser usado.

1. Inicie sessão no servidor SAP S/4HANA ou SAP ECC e abra **o Navegador de Objetos** (transação SE80).

2. Selecione opções **Pacote** da lista e introduza um nome para o novo pacote (por exemplo, Z \_ MITI) e, em seguida, prima o botão **Display**.

3. Selecione **Sim** na janela **'Criar Embalagem'.** Consequentemente, abre-se um **construtor de pacotes de janelas: Crie pacote.** Introduza valor no campo **De Descrição Curta** e selecione o ícone **Continuar.**

4. Selecione **Pedidos Próprios** na **janela de pedido local workbench** local. Selecione o pedido **de desenvolvimento.**

### <a name="create-a-function-group"></a>Criar um Grupo de Função

No Object Navigator selecione Grupo de **Função** da lista e digite o seu nome no campo de entrada abaixo (por exemplo, Z \_ MITI \_ FGROUP). Selecione o ícone **Ver.**

1. Na janela **Create Object,** selecione **sim** para criar um novo grupo de funções.

2. Especifique uma descrição adequada no campo **de texto curto** e **pressione** o botão Guardar .

3. Escolha um pacote que foi preparado na etapa anterior **Criar um Pacote** e selecione **Save**.

4. Confirme um pedido pressionando o ícone **Continue**.

5. Ativar o Grupo de Funções.

### <a name="create-the-abap-function-module"></a>Criar o Módulo de Função ABAP

1. Uma vez criado o grupo de funções, selecione-o.

2. Clique com o botão direito no nome do grupo de funções no navegador do repositório e selecione **Criar,** em seguida, **Módulo de Função**.

3. No campo **Módulo de Função,** insira `Z_MITI_DOWNLOAD` . Preencha **entrada de texto curto** com descrição adequada.

Quando o módulo tiver sido criado, especifique as seguintes informações:

1. Navegue para o **separador Atributos.**

2. Selecione **o tipo** de processamento como módulo **de função ativado remotamente.**

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Opção de fontes de registo - Módulo de função Remote-Enabled" border="true":::

3. Navegue para o **separador código Fonte.** Existem duas formas de implementar código para a função:

   a. A partir do menu principal, faça o upload do ficheiro de texto [Z \_ MITI \_ DOWNLOAD.](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) Para tal, **selecione Utilities**, **Mais Utilitários,** depois **Carregar/Descarregar,** em seguida, **Carregar**.

   b. Em alternativa, abra o ficheiro, copie o seu conteúdo e cole-o na área **do código Fonte.**

4. Navegue no **separador Import e** crie os seguintes parâmetros:

   a.  P \_ TIPO DE ÁREA DD02L-TABNAME (Opcional = Verdadeiro)

   b.  *P \_ \_CORDA TIPO CAMINHO LOCAL* (Opcional = Verdade)

   c.  *P \_ IDIOMA TIPO L001TAB-DADOS PADRÃO \' E\'*

   d.  *ROWSKIPS TIPO TÃO \_ INT PADRÃO 0*

   e.  *TIPO ROWCOUNT SO \_ INT DEFAULT 0*

   > [!Note]
   > Escolha **o Valor de Passe** para todos eles

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Opção de fontes de registo - Parâmetros de importação" border="true":::

5. Navegue no separador "Tabelas" e defina o seguinte:

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="Opções de fontes de registo - Separador tabelas" border="true":::

6. Navegue no separador **Exceções** e defina a seguinte exceção: `E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="Opções de fontes de registo - Separador exceções" border="true":::

7. Guarde a função (prima ctrl+S ou escolha **o Módulo de Função,** depois **guarde** no menu principal).

8. Clique em **Ativar** o ícone na barra de ferramentas (ctrl+F3) e selecione  **Continue** na janela de diálogo. Se solicitado, deve selecionar as incluições geradas para serem ativadas juntamente com o módulo de função principal.

### <a name="testing-the-function"></a>Testar a Função

Quando todos os passos anteriores estiverem concluídos, siga os passos abaixo para testar a função:

1. Abra \_ o \_ módulo de função DOWNLOAD Z MITI.

2. Escolha **o Módulo de Função** e, em seguida, **teste,** em seguida, teste o módulo de **função** a partir do menu principal (ou prima F8).

3. Introduza um caminho para a pasta no sistema de ficheiros local no parâmetro P \_ LOCAL PATH e prima \_ **executar** o ícone na barra de ferramentas (ou prima F8).

4. Coloque o nome da área de interesse no campo P \_ AREA se um ficheiro com metadados tiver de ser descarregado ou atualizado. Quando a função terminar de funcionar, a pasta indicada no parâmetro P \_ LOCAL PATH deve conter \_ vários ficheiros com metadados no interior. Os nomes dos ficheiros imitam áreas que podem ser especificadas no campo P \_ AREA.

A função terminará a sua execução e os metadados serão descarregados muito mais rapidamente em caso de o lançar na máquina que tem ligação de rede de alta velocidade com o servidor SAP S/4HANA ou ECC.

## <a name="next-steps"></a>Passos seguintes

- [Registar e digitalizar fonte ECC SAP](register-scan-sapecc-source.md)
- [Registar e digitalizar fonte SAP S/4HANA](register-scan-saps4hana-source.md)
