---
title: Restaurar app a partir de um instantâneo
description: Aprenda a restaurar a sua aplicação a partir de uma foto. Recupere de perdas inesperadas de dados no nível Premium com as cópias-sombra automáticas.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: b17a49535541c8f75f65cdbe9986a895427f3877
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78255139"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Restaurar uma aplicação em Azure a partir de um instantâneo
Este artigo mostra-lhe como restaurar uma aplicação no [Azure App Service](../app-service/overview.md) a partir de um instantâneo. Pode restaurar a sua aplicação para um estado anterior, com base numa das fotos da sua aplicação. Não é necessário ativar a cópia de segurança de instantâneos, a plataforma guarda automaticamente uma imagem instantânea de todas as aplicações para fins de recuperação de dados.

Os instantâneos são cópias de sombra incremental, e oferecem várias vantagens sobre [cópias de segurança regulares:](manage-backup.md)
- Não há erros de cópia de ficheiros devido a fechaduras de ficheiros.
- Sem limitação do tamanho do armazenamento.
- Não é necessária qualquer configuração.

Restaurar a partir de instantâneos está disponível para aplicações em nível **Premium** ou superior. Para obter informações sobre o dimensionamento da sua aplicação, consulte [Scale up uma aplicação no Azure](manage-scale-up.md).

## <a name="limitations"></a>Limitações

- A funcionalidade encontra-se atualmente em pré-visualização.
- Só pode restaurar a mesma aplicação ou uma ranhura pertencente a essa aplicação.
- O Serviço de Aplicações para a aplicação alvo ou a ranhura de destino durante a restauração.
- O Serviço de Aplicações mantém três meses de instantâneos para fins de recuperação de dados da plataforma.
- Só pode restaurar as fotos nos últimos 30 dias.
- Os serviços de aplicações que estão a funcionar num App Service Environment não suportam instantâneos.
 

## <a name="restore-an-app-from-a-snapshot"></a>Restaurar uma aplicação a partir de um instantâneo

1. Na página **Definições** da sua aplicação no [portal Azure,](https://portal.azure.com)clique em **Backups** para exibir a página **Backups.** Em seguida, clique em **Restaurar** sob a secção **Snapshot(Pré-visualização).**
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Na página **Restaurar,** selecione o instantâneo para restaurar.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Especifique o destino para a restauração da aplicação no **destino Restaurar**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Se escolher **a Overwrite**, todos os dados existentes no sistema de ficheiros atual da sua aplicação são apagados e substituídos. Antes de clicar em **OK,** certifique-se de que é o que quer fazer.
   > 
   > 
      
   > [!Note]
   > Devido às limitações técnicas atuais, só é possível restaurar as aplicações na mesma unidade de escala. Esta limitação será removida numa futura versão.
   > 
   > 
   
    Pode selecionar a **App Existente** para restaurar uma ranhura. Antes de utilizar esta opção, já deveria ter criado uma ranhura na sua aplicação.

4. Pode optar por restaurar a configuração do seu site.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Clique em **OK**.
