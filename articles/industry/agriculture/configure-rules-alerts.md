---
title: Configurar regras e gerenciar alertas no Azure FarmBeats
description: Descreve como configurar regras e gerenciar alertas no FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02df4dd97c9a98ff6dd1c42957884fa5ca64365f
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798534"
---
# <a name="configure-rules-and-manage-alerts-in-farmbeats"></a>Configurar regras e gerenciar alertas no FarmBeats

O Azure FarmBeats permite que você crie regras com base na lógica de negócios, além dos dados do sensor que fluem dos sensores e dispositivos implantados em seu farm. As regras disparam alertas no sistema sempre que os valores do sensor ultrapassarem um valor limite. Ao exibir e analisar os alertas criados após os valores de limite, você pode agir rapidamente sobre problemas e obter as soluções necessárias.

## <a name="create-rule"></a>Criar regra

1. Na home page, vá para **regras**.
2. Selecione **nova regra**. A janela nova regra é exibida.

    ![Batidas no farm de projetos](./media/configure-rules-and-alerts/new-rule-1.png)

3. Insira o **nome da regra** e a **Descrição da regra** e, em seguida, selecione um farm no menu suspenso **selecionar Farm** .
4. Digite o nome do farm para selecionar a seção farm e **condições** aparece na mesma janela.  

    ![Batidas no farm de projetos](./media/configure-rules-and-alerts/new-rule-condition-1.png)

5. Em **condições**, insira os valores para **medida**, **operador** e **valor**.
6. Digite o nome da medida no menu suspenso **medida** .
7. Selecione **+ Adicionar condição** para adicionar mais condições à regra.
8. Selecione o **nível de severidade**.
9. Em **ação**, alterne o botão de alternância **email habilitado** para habilitar alertas de email.

    ![Batidas no farm de projetos](./media/configure-rules-and-alerts/new-rule-email-1.png)

10. Insira os **endereços de email** para os quais você deseja enviar o alerta por email, juntamente com o **assunto do email** e as **observações adicionais**.  
11. No **status da regra**, alterne para o botão de alternância **habilitado** para habilitar ou desabilitar a regra.
    Você pode exibir o número de dispositivos que serão afetados pela regra.
12. Selecione **aplicar** para criar a regra.

## <a name="view-rule"></a>Exibir regra

A página do **farm** exibe a lista de regras disponíveis. Selecione um **nome de regra**. Uma janela exibe os seguintes detalhes que são aplicáveis para a regra selecionada:
 - Nome da regra
 - Link para o farm ao qual a regra está associada
 - Data de criação
 - Data da última atualização
 - Nível de gravidade
 - Status da regra
 - Lista de condições  
 - Número de dispositivos afetados pela regra

    ![Batidas no farm de projetos](./media/configure-rules-and-alerts/view-rule-1.png)

## <a name="edit-rule"></a>Editar regra

Para editar uma regra, siga estas etapas:

1. Na home page, selecione **regras** no menu de navegação à esquerda.
   A janela regras é exibida.
2. Selecione a regra para a qual você deseja editar.

    ![Batidas no farm de projetos](./media/configure-rules-and-alerts/edit-rule-action-bar-1.png)

3. Selecione **Editar** na barra de ações, a janela **Editar regra** é exibida.

    ![Batidas no farm de projetos](./media/configure-rules-and-alerts/edit-rule-one-1.png)

4. Altere o **nome da regra**e a **Descrição da regra** e, em seguida, selecione um farm no menu suspenso **selecionar Farm** .
5. Digite o nome do farm para selecionar o farm e as **condições** aparecerão na mesma janela.  
6. Em **condições**, edite **Measure**, **Operator** e **Value**.
7. Digite o nome da medida no menu suspenso **medida** .
8. Selecione **+ Adicionar condição** para adicionar/editar condições para as regras.

    ![Batidas no farm de projetos](./media/configure-rules-and-alerts/edit-rule-two-1.png)

9.  Selecione o **nível de severidade**.  
10. Em **ação**, alterne o botão de alternância **email habilitado** para habilitar alertas de email.
11. Edite os **endereços de email** para os quais você deseja enviar o alerta por email, juntamente com o assunto do **email** e as **observações adicionais**.  
12. No **status da regra**, alterne para o botão de alternância **habilitado** para habilitar ou desabilitar a regra.
Você pode exibir o número de dispositivos que serão afetados por essa regra.
13. Selecione **aplicar** para editar a regra.

## <a name="change-rule-status"></a>Alterar status da regra

Para alterar o status de uma regra, siga estas etapas:

1. Na home page, selecione **regras** no menu de navegação à esquerda. A janela regras é exibida.
2. Selecione a regra para a qual você deseja alterar o status.

    ![Batidas no farm de projetos](./media/configure-rules-and-alerts/change-status-rule-action-bar-1.png)

3. Selecione **Alterar status** na barra de ação. A janela **status da alteração** é exibida.

    ![Batidas no farm de projetos](./media/configure-rules-and-alerts/rule-change-status-1.png)

3. Altere o status da regra usando o botão de alternância **Alterar status** .
   Você pode exibir o número de dispositivos que serão afetados pela regra.
4. Selecione **aplicar** para alterar o status da regra.

## <a name="delete-rule"></a>Excluir regra

Para excluir uma regra, siga estas etapas:

1. Na home page, selecione **regras** no menu de navegação à esquerda. A janela regras é exibida.
2. Selecione a regra para a qual você deseja excluir.

    ![Batidas no farm de projetos](./media/configure-rules-and-alerts/delete-rule-action-bar-1.png)

3. Selecione **excluir** na barra de ação.

    ![Batidas no farm de projetos](./media/configure-rules-and-alerts/delete-rule-1.png)

4. A caixa de diálogo **excluir regra** é exibida. Selecione **Eliminar**.
