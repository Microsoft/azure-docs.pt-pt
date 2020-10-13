---
title: Restaurar app a partir de um instantâneo
description: Saiba como restaurar a sua aplicação a partir de uma foto instantânea. Recuperar de perda inesperada de dados no nível Premium com as cópias de sombra automáticas.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: f7edb632559dc8da2de32c58d994a7c51b1b09e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169980"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Restaurar uma aplicação em Azure a partir de um instantâneo
Este artigo mostra-lhe como restaurar uma aplicação no [Azure App Service](../app-service/overview.md) a partir de uma foto. Pode restaurar a sua aplicação para um estado anterior, com base numa das fotos da sua aplicação. Não é necessário ativar a cópia de segurança das imagens, a plataforma guarda automaticamente uma imagem de todas as aplicações para efeitos de recuperação de dados.

Os instantâneos são cópias de sombra incrementais, e oferecem várias vantagens sobre [cópias de segurança regulares:](manage-backup.md)
- Não há erros de cópia de ficheiros devido a bloqueios de ficheiros.
- Sem limitação do tamanho do armazenamento.
- Não é necessária configuração.

Restaurar a partir de instantâneos está disponível para aplicações em execução em nível **Premium** ou superior. Para obter informações sobre o escalonamento da sua aplicação, consulte [Scale up uma aplicação em Azure](manage-scale-up.md).

## <a name="limitations"></a>Limitações

- A funcionalidade encontra-se atualmente em pré-visualização.
- Só é possível restaurar a mesma aplicação ou uma ranhura pertencente a essa aplicação.
- O Serviço de Aplicações para a aplicação alvo ou a ranhura do alvo enquanto faz a restauração.
- O Serviço de Aplicações mantém três meses de instantâneos para fins de recuperação de dados da plataforma.
- Só pode restaurar as fotos nos últimos 30 dias.
- Os Serviços de Aplicações em execução num Ambiente de Serviço de Aplicações não suportam instantâneos.
 

## <a name="restore-an-app-from-a-snapshot"></a>Restaurar uma aplicação a partir de um instantâneo

1. Na página **definições** da sua aplicação no [portal Azure,](https://portal.azure.com)clique em **Backups** para exibir a página **de Backups.** Em seguida, clique em **Restaurar** na secção **Snapshot (Preview).**
   
    ![Screenshot que mostra como restaurar uma aplicação a partir de uma cópia de segurança instantânea.](./media/app-service-web-restore-snapshots/1.png)

2. Na página **'Restaurar',** selecione o instantâneo para restaurar.
   
    ![Screenshot que mostra como selecionar o instantâneo para restaurar. ](./media/app-service-web-restore-snapshots/2.png)
   
3. Especifique o destino para a restauração da aplicação no **destino Restaurar.**
   
    ![Screenshot que mostra como especificar o destino de restauração.](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Se escolher **Overwrite,** todos os dados existentes no sistema de ficheiros atuais da sua aplicação são apagados e substituídos. Antes de clicar **em OK,** certifique-se de que é o que quer fazer.
   > 
   > 
      
   > [!Note]
   > Devido às limitações técnicas atuais, só é possível restaurar as aplicações na mesma unidade de escala. Esta limitação será removida numa futura libertação.
   > 
   > 
   
    Pode selecionar **a App Existente** para restaurar uma ranhura. Antes de utilizar esta opção, já deveria ter criado uma ranhura na sua aplicação.

4. Pode optar por restaurar a configuração do seu site.
   
    ![Screenshot que mostra como restaurar a configuração do site.](./media/app-service-web-restore-snapshots/4.png)

5. Clique em **OK**.
