---
title: Restaurar aplicativo de um instantâneo
description: Saiba como restaurar seu aplicativo de um instantâneo. Recupere-se da perda de dados inesperada na camada Premium com as cópias de sombra automáticas.
author: ahmedelnably
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.author: aelnably
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: e5b48e05d6bd9a310b4527300126464c4b755c8d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671147"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Restaurar um aplicativo no Azure de um instantâneo
Este artigo mostra como restaurar um aplicativo no [serviço Azure app](../app-service/overview.md) de um instantâneo. Você pode restaurar seu aplicativo para um estado anterior, com base em um dos instantâneos do seu aplicativo. Você não precisa habilitar o backup de instantâneos, a plataforma salva automaticamente um instantâneo de todos os aplicativos para fins de recuperação de dados.

Os instantâneos são cópias de sombra incrementais e oferecem várias vantagens em relação aos [backups](manage-backup.md)regulares:
- Nenhum erro de cópia de arquivo devido a bloqueios de arquivo.
- Nenhuma limitação de tamanho de armazenamento.
- Nenhuma configuração é necessária.

A restauração a partir de instantâneos está disponível para aplicativos em execução na camada **Premium** ou superior. Para obter informações sobre como escalar verticalmente seu aplicativo, consulte [escalar verticalmente um aplicativo no Azure](manage-scale-up.md).

## <a name="limitations"></a>Limitações

- O recurso está atualmente em visualização.
- Você só pode restaurar para o mesmo aplicativo ou para um slot que pertença a esse aplicativo.
- O serviço de aplicativo interrompe o aplicativo de destino ou o slot de destino durante a restauração.
- O serviço de aplicativo mantém três meses de instantâneos para fins de recuperação de dados da plataforma.
- Você só pode restaurar instantâneos nos últimos 30 dias.
- Os serviços de aplicativos em execução em um Ambiente do Serviço de Aplicativo não dão suporte a instantâneos.
 

## <a name="restore-an-app-from-a-snapshot"></a>Restaurar um aplicativo de um instantâneo

1. Na página **configurações** do seu aplicativo no [portal do Azure](https://portal.azure.com), clique em **backups** para exibir a página **backups** . Em seguida, clique em **restaurar** na seção **instantâneo (visualização)** .
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Na página **restaurar** , selecione o instantâneo a ser restaurado.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Especifique o destino para a restauração do aplicativo no **destino da restauração**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Se você escolher **substituir**, todos os dados existentes no sistema de arquivos atual do aplicativo serão apagados e substituídos. Antes de clicar em **OK**, verifique se é o que você deseja fazer.
   > 
   > 
      
   > [!Note]
   > Devido às limitações técnicas atuais, você só pode restaurar para aplicativos na mesma unidade de escala. Essa limitação será removida em uma versão futura.
   > 
   > 
   
    Você pode selecionar o **aplicativo existente** para restaurar em um slot. Antes de usar essa opção, você já deve ter criado um slot em seu aplicativo.

4. Você pode optar por restaurar a configuração do site.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Clique em **OK**.
