---
title: Restaurar a partir de cópia de segurança - serviço de aplicações do Azure
description: Saiba como restaurar a aplicação a partir de um instantâneo.
services: app-service
documentationcenter: ''
author: ahmedelnably
manager: cfowler
editor: ''
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: multiple
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: ed659e95289665b6ce63ba6961e9f63650b4accf
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617548"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Restaurar uma aplicação no Azure a partir de um instantâneo
Este artigo mostra como restaurar uma aplicação no [App Service do Azure](../app-service/overview.md) partir de um instantâneo. Pode restaurar a sua aplicação para um estado anterior, com base em um dos instantâneos da sua aplicação. Não é necessário ativar a cópia de segurança de instantâneos, a plataforma guarda automaticamente um instantâneo de todas as aplicações para fins de recuperação de dados.

Os instantâneos são cópias de sombra de incremental e oferecem algumas vantagens em relação a regular [cópias de segurança](manage-backup.md):
- Não existem erros de cópia de ficheiros devido a bloqueios de arquivo.
- Sem limite de tamanho de armazenamento.
- Nenhuma configuração necessária.

Restaurar a partir de instantâneos está disponível para aplicações em execução no **Premium** escalão ou superior. Para obter informações sobre como aumentar verticalmente a sua aplicação, consulte [aumentar verticalmente uma aplicação no Azure](web-sites-scale.md).

## <a name="limitations"></a>Limitações

- A funcionalidade está atualmente em pré-visualização.
- Só pode restaurar para a mesma aplicação ou para um bloco que pertencem a essa aplicação.
- Serviço de aplicação para a aplicação de destino ou a ranhura de destino durante o processo de restauro.
- Serviço de aplicações mantém três meses que vale a pena de instantâneos para fins de recuperação de dados de plataforma.
- Só pode restaurar instantâneos para os últimos 30 dias.
- Serviços de aplicações em execução num ambiente de serviço de aplicações não suportam instantâneos.
 

## <a name="restore-an-app-from-a-snapshot"></a>Restaurar uma aplicação a partir de um instantâneo

1. Na **definições** página da sua aplicação no [portal do Azure](https://portal.azure.com), clique em **cópias de segurança** para exibir o **cópias de segurança** página. Em seguida, clique em **restaurar** sob a **Snapshot(Preview)** secção.
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Na **restaurar** , selecione o instantâneo para restaurar.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Especifique o destino para o restauro de aplicação no **destino de restauro**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Se escolher **substituir**, todos os dados existentes no sistema de ficheiros atual da sua aplicação são eliminados e substituídos. Antes de clicar em **OK**, certifique-se de que é o que deseja fazer.
   > 
   > 
      
   > [!Note]
   > Devido a limitações técnicas atuais, apenas pode restaurar para aplicações na mesma unidade de escala. Esta limitação será removida numa versão futura.
   > 
   > 
   
    Pode selecionar **aplicação existente** para restaurar para um bloco. Antes de utilizar esta opção, deve já tiver criado um bloco na sua aplicação.

4. Pode optar por restaurar a configuração do seu site.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Clique em **OK**.
