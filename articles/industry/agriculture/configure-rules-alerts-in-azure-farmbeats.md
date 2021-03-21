---
title: Configurar regras e gerir alertas
description: Descreve como configurar regras e gerir alertas em FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: a04f973cbfa3a68016065f50e9e2ff4f7566da94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182932"
---
# <a name="configure-rules-and-manage-alerts"></a>Configurar regras e gerir alertas

O Azure FarmBeats permite criar regras baseadas na lógica do negócio, além dos dados dos sensores que fluem dos sensores e dispositivos implantados na sua quinta. As regras disparam alertas no sistema sempre que os valores dos sensores cruzam um valor limiar. Ao visualizar e analisar os alertas criados após os valores-limiar, pode agir rapidamente sobre quaisquer questões e obter soluções necessárias.

## <a name="create-rule"></a>Criar regra

1. Na página inicial, vá ao **Regimento.**
2. Selecione **Nova Regra**. A janela New Rule aparece.

    ![Screenshot que realça o botão Nova Regra e a secção Nova Regra.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Introduza o **Nome da Regra** e a **Descrição da Regra** e, em seguida, selecione uma quinta no menu Select **Farm** drop-down.
4. Digite o nome da sua quinta para selecionar a secção de quinta e **condições** aparece na mesma janela.  

    ![Screenshot que destaca a secção Condições.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. Em **Condições,** insira os valores de **Medida,** **Operador** e **Valor.**
6. Digite o nome da medida no menu de entrega da **medida.**
7. **Selecione +Adicionar Condição** para adicionar mais condições à regra.
8. Selecione o **nível de Severidade**.
9. Em **Ação,** ligue o **botão de e-mail ativado** para ativar alertas de e-mail.

    ![Screenshot que mostra a opção ativada por E-mail.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Insira os **endereços de e-mail** para os quais pretende enviar o alerta de e-mail, juntamente com o **Assunto de E-mail** e **Notas Adicionais.**  
11. No **Estado de Regra,** ligue o botão de alternação **ativado** para ativar ou desativar a regra.
    Pode ver o número de dispositivos que serão afetados pela regra.
12. **Selecione Aplicar** para criar a regra.

## <a name="view-rule"></a>Ver regra

A página **Farm** apresenta a lista de regras disponíveis. Selecione um **nome de regra**. Uma janela apresenta os seguintes detalhes que são aplicáveis à regra selecionada:
 - Nome da regra
 - Ligação à quinta à qual a regra está associada
 - Data criada
 - Última data atualizada
 - Nível de gravidade
 - Estatuto de regra
 - Lista de condições  
 - Número de dispositivos afetados pela regra

    ![Screenshot que mostra o ecrã Detalhes de Regras.](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Editar regra

Para editar uma regra, siga estes passos:

1. Na página inicial, selecione **Regras** do menu de navegação à esquerda.
   As regras mostram a janela.
2. Selecione a regra para a qual pretende editar.

    ![Screenshot que mostra a regra selecionada.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Selecione **Editar** a partir da barra de ação, a janela **'Regra de Edição'** aparece.

    ![Screenshot que mostra o ecrã da Regra de Edição.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Altere o **Nome da Regra** e a **Descrição da Regra** e, em seguida, selecione uma quinta no menu Select **Farm** drop-down.
5. Digite o nome da sua quinta para selecionar a quinta e **as condições** aparecem na mesma janela.  
6. Em **Condições,** editar **Medida,** **Operador** e **Valor.**
7. Digite o nome da medida no menu de entrega da **medida.**
8. **Selecione +Adicionar Condições** para adicionar/editar as condições às regras.

    ![Screenshot que realça o botão Adicionar Condição.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Selecione o **Nível de Severidade**.  
10. Em **Ação,** ligue o **botão de e-mail ativado** para ativar alertas de e-mail.
11. Edite os **endereços de e-mail** que pretende enviar o alerta de e-mail, juntamente com o **Assunto de E-mail** e **Notas Adicionais.**  
12. No **Estado de Regra,** ligue o botão de alternação **ativado** para ativar ou desativar a regra.
Pode ver o número de dispositivos que serão afetados por esta regra.
13. **Selecione Aplicar** para editar a regra.

## <a name="change-rule-status"></a>Alterar o estado de regra

Para alterar o estado de uma regra, siga estes passos:

1. Na página inicial, selecione **Regras** do menu de navegação à esquerda. As regras mostram a janela.
2. Selecione a regra para a qual pretende alterar o estado.

    ![Screenshot que mostra o botão 'Alterar Estado'.](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Selecione **Alterar Estado** da barra de ação. A janela **'Alterar Estado'** apresenta-se.

    ![Screenshot que mostra o ecrã change status.](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Alterar o estado de regra utilizando o botão **Change Status** toggle.
   Pode ver o número de dispositivos que serão afetados pela Regra.
4. **Selecione Aplicar** para alterar o estado de regra.

## <a name="delete-rule"></a>Eliminar Regra

Para eliminar uma regra, siga estes passos:

1. Na página inicial, selecione **Regras** do menu de navegação à esquerda. As regras mostram a janela.
2. Selecione a regra para a qual pretende eliminar.

    ![Screenshot que realça o botão Eliminar.](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. **Selecione Excluir** da barra de ação.

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. A caixa de diálogo **"Excluir regra"** apresenta. Selecione **Eliminar**.
