---
title: Tutorial como transferir e utilizar um script para acessar o início de sessão registos | Documentos da Microsoft
description: Saiba como transferir e utilizar um script do PowerShell para aceder aos registos de início de sessão.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4afe0c73-aee8-47f1-a6cb-2d71fd6719d1
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: aedbc625bedcbe66b43b66ce96e1b17746b9a47c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531294"
---
# <a name="tutorial-how-to-download-and-use-a-script-to-access-sign-in-logs"></a>Tutorial: Como transferir e utilizar um script para aceder aos registos de início de sessão

Pode transferir os dados de atividades de início de sessão se pretender trabalhar com eles fora do portal do Azure. O **transferir** opção no portal do Azure cria um ficheiro CSV dos 5000 registos mais recentes. Se precisar de mais flexibilidade, por exemplo, para baixar mais de 5000 registos ao mesmo tempo, ou para transferir os registos em intervalos agendados, pode utilizar o **Script** botão para gerar um script do PowerShell para transferir os seus dados.

Neste tutorial, saiba como gerar um script para transferir todos os início de sessão registos das últimas 24 horas e agendá-la para executar todos os dias. 

## <a name="prerequisites"></a>Pré-requisitos

É necessário

* Um inquilino do Azure Active Directory com uma licença premium (P1/P2). Tenha em atenção que, se não tem quaisquer dados de atividades antes da atualização, demorará alguns dias para os dados sejam apresentados nos relatórios depois de atualizar para uma licença premium. 
* Um utilizador, o que está a **administrador global**, **administrador de segurança**, **leitor de segurança** ou **leitor de relatório** função para o inquilino. Além disso, qualquer usuário pode acessar seus próprios inícios de sessão. 

## <a name="tutorial"></a>Tutorial

1. Navegue para o [portal do Azure](https://portal.azure.com) e selecione o seu diretório.
2. Selecione **do Azure Active Directory** e selecione **inícios de sessão** partir os **monitorização** secção. 
3. Utilize o **intervalo de datas** filtro de lista pendente e selecione **24 horas** para obter dados das últimas 24 horas. 
4. Selecione **aplicar** e certifique-se de que o filtro é aplicado conforme esperado. 
5. Selecione **Script** no menu superior para transferir o script do Powershell com os filtros aplicados.

     ![Transferir script](./media/tutorial-signin-logs-download-script/download-script.png)
     
6. Abra o **agendador de tarefas** aplicação no seu computador Windows e selecione **criar tarefa básica**.
7. Introduza um nome e descrição para a tarefa e clique em **seguinte**.
8. Selecione o **diária** botão de opção para permitir que a tarefa ser executada diariamente e introduza a data de início e hora.
9. No menu ação, selecione **iniciar um programa** e selecione o script transferido e selecione **próxima**. 
10. Reveja a tarefa agendada e selecione **concluir** para criar a tarefa.

     ![Criar tarefa](./media/tutorial-signin-logs-download-script/create-task.png)

Agora, a tarefa irá executar todos os dias e guarde os registos de início de sessão das últimas 24 horas num ficheiro no formato **AAD_SignInReport_YYYYMMDD_HHMMSS.csv**. Também pode editar o script do PowerShell transferido para guardá-lo num nome de ficheiro diferentes ou para modificar o número de registos transferido. 

## <a name="next-steps"></a>Passos Seguintes

* [Políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md)
* [Getting started with the Azure Active Directory reporting API](concept-reporting-api.md) (Introdução à API de relatórios do Azure Active Directory)
* [Acesso a API de relatórios com certificados](tutorial-access-api-with-certificates.md)
