---
title: Configurar regras e gerir alertas
description: Descreve como configurar regras e gerir alertas em FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482988"
---
# <a name="configure-rules-and-manage-alerts"></a>Configurar regras e gerir alertas

O Azure FarmBeats permite criar regras baseadas na lógica do negócio, além dos dados de sensores que desaguam dos sensores e dispositivos implantados na sua quinta. As regras desencadeiam alertas no sistema sempre que os valores dos sensores ultrapassem um valor limiar. Ao visualizar e analisar os alertas criados após os valores limiares, pode agir rapidamente sobre quaisquer questões e obter soluções necessárias.

## <a name="create-rule"></a>Criar regra

1. Na página inicial, vá ao **Regimento.**
2. Selecione **Nova Regra**. A janela new rule exibe.

    ![Batidas da Fazenda do Projeto](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Introduza o nome da **regra** e **a descrição da regra** e, em seguida, selecione uma quinta do menu de entrega da Fazenda **Select.**
4. Digite o seu nome de fazenda para selecionar a secção de quinta e **condições** aparece na mesma janela.  

    ![Batidas da Fazenda do Projeto](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. Em **Condições,** insira os valores de **Medida,** **Operador** e **Valor.**
6. Digite o nome da medida no menu de entrega de **medidas.**
7. Selecione **+Adicionar Condição** para adicionar mais condições à regra.
8. Selecione o **nível de gravidade**.
9. Em **Ação,** ligue o botão de alternância **ativado** pelo email para ativar os alertas de e-mail.

    ![Batidas da Fazenda do Projeto](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Insira os endereços de **e-mail** para os quais pretende enviar o alerta de e-mail, juntamente com o Assunto do **E-mail** e **Notas Adicionais**.  
11. No Estado da **Regra,** ligue o botão de alternância **ativado** para ativar ou desativar a regra.
    Pode ver o número de dispositivos que serão afetados pela regra.
12. Selecione **Aplicar** para criar a regra.

## <a name="view-rule"></a>Ver regra

A página **Farm** apresenta a lista de regras disponíveis. Selecione um nome de **regra**. Uma janela apresenta os seguintes detalhes aplicáveis à regra selecionada:
 - Nome da regra
 - Ligação à quinta à qual a regra está associada
 - Data criada
 - Última data atualizada
 - Nível de gravidade
 - Estatuto de regra
 - Lista de condições  
 - Número de dispositivos afetados pela regra

    ![Batidas da Fazenda do Projeto](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Editar regra

Para editar uma regra, siga estes passos:

1. Na página inicial, selecione **Regras** do menu de navegação à esquerda.
   As regras mostram as janelas.
2. Selecione a regra para a qual pretende editar.

    ![Batidas da Fazenda do Projeto](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. **Selecione Editar** a partir da barra de ação, a janela **'Regra de Edição'.**

    ![Batidas da Fazenda do Projeto](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Altere o Nome da **Regra**e **a Descrição da Regra** e, em seguida, selecione uma quinta do menu de entrega da Fazenda **Select.**
5. Digite o seu nome de fazenda para selecionar a quinta e **as condições** aparecem na mesma janela.  
6. Em **Condições**, **editar Medida,** **Operador** e **Valor.**
7. Digite o nome da medida no menu de entrega de **medidas.**
8. Selecione **+Adicionar Condição** para adicionar/editar condições às regras.

    ![Batidas da Fazenda do Projeto](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Selecione o **Nível de Gravidade**.  
10. Em **Ação,** ligue o botão de alternância **ativado** pelo email para ativar os alertas de e-mail.
11. Editar os **endereços de e-mail** que pretende enviar o alerta de e-mail, juntamente com o Assunto do **E-mail** e **Notas Adicionais.**  
12. No Estado da **Regra,** ligue o botão de alternância **ativado** para ativar ou desativar a regra.
Pode ver o número de dispositivos que serão afetados por esta regra.
13. Selecione **Aplicar** para editar a regra.

## <a name="change-rule-status"></a>Alterar o estado da regra

Para alterar o estatuto de regra, siga estes passos:

1. Na página inicial, selecione **Regras** do menu de navegação à esquerda. As regras mostram as janelas.
2. Selecione a regra para a qual pretende alterar o estado.

    ![Batidas da Fazenda do Projeto](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Selecione **Alterar o Estado** da barra de ação. A janela **Change Status** exibe.

    ![Batidas da Fazenda do Projeto](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Altere o estado da regra utilizando o botão de alternância do **Estado de alteração.**
   Pode ver o número de dispositivos que serão afetados pela Regra.
4. Selecione **Aplicar** para alterar o estado da regra.

## <a name="delete-rule"></a>Eliminar Regra

Para eliminar uma regra, siga estes passos:

1. Na página inicial, selecione **Regras** do menu de navegação à esquerda. As regras mostram as janelas.
2. Selecione a regra para a qual pretende eliminar.

    ![Batidas da Fazenda do Projeto](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. **Selecione Eliminar** a partir da barra de ação.

    ![Batidas da Fazenda do Projeto](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. A caixa de diálogo **Delete Rule** exibe. Selecione **Eliminar**.
