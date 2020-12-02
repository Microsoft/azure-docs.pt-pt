---
title: Start-up os OS – Computador reiniciou inesperadamente ou encontrou um erro inesperado
description: Este artigo fornece medidas para resolver problemas em que o VM experimenta um inesperado reinício ou erro durante a instalação do Windows.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: cfeb040893ae2be5842959ed8458bd713bebe6ee
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512142"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Start-up os OS – Computador reiniciou inesperadamente ou encontrou um erro inesperado

Este artigo fornece medidas para resolver problemas em que a máquina virtual (VM) experimenta um inesperado reinício ou erro durante a instalação do Windows.

## <a name="symptom"></a>Sintoma

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem mostra a instalação do Windows falhando com o seguinte erro:

**O computador recomeçou inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode prosseguir. Para instalar o Windows, clique em "OK" para reiniciar o computador e, em seguida, reinicie a instalação.**

![Erro enquanto a Instalação do Windows está em andamento: O computador reiniciou inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode prosseguir. Para instalar o Windows, clique em "OK" para reiniciar o computador e, em seguida, reinicie a instalação.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Erro quando a instalação da instalação do Windows está a iniciar serviços: O computador reiniciou inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode prosseguir. Para instalar o Windows, clique em "OK" para reiniciar o computador e, em seguida, reinicie a instalação.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Causa

A máquina está a tentar fazer uma bota inicial de uma [imagem generalizada](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), mas encontra problemas devido a um ficheiro de resposta personalizado (Unattend.xml) a ser processado. **Os ficheiros de resposta personalizados não são suportados no Azure**. 

O ficheiro de resposta é um ficheiro XML especial que contém definições e valores de definição para as definições de configuração que pretende automatizar durante a instalação de uma instalação do sistema operativo Windows Server. As opções de configuração incluem instruções sobre como dividir discos, onde encontrar a imagem do Windows a ser instalada, chaves do produto a aplicar e outros comandos que gostaria de executar.

Mais uma vez, os ficheiros de resposta personalizados não são suportados no Azure. Assim, esta situação ocorre quando uma imagem foi preparada para ser usada em Azure, mas especificou um ficheiro Unattend.xml personalizado utilizando **o SYSPREP** com uma bandeira semelhante ao seguinte comando:

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

Em Azure, utilize a opção **Enter System Out-of-Box Experience (OOBE)** na **ferramenta de preparação do sistema GUI**, ou utilize em vez do ficheiro `sysprep /oobe` Unattend.xml.

Este problema é criado na maioria das vezes enquanto você está usando sysprep com um VM no local para carregar um VM generalizado para Azure. Nesta situação, você também pode estar interessado em como carregar corretamente um VM generalizado.

## <a name="solution"></a>Solução

### <a name="do-not-use-unattendxml"></a>Não utilize Unattend.xml

Para corrigir este problema, siga [a orientação do Azure sobre a preparação/captura de uma imagem](../windows/upload-generalized-managed.md) e prepare uma nova imagem generalizada. Durante o sysprep, **não utilize `/unattend:<your file’s name>` a bandeira**. Em vez disso, utilize apenas as bandeiras abaixo:

`sysprep /oobe /generalize /shutdown`

- Fora da experiência fora da caixa (OOBE) é a definição suportada para VMs Azure.

Pode também utilizar a **ferramenta de preparação** do sistema GUI para realizar a mesma tarefa que o comando acima, selecionando as opções apresentadas abaixo:

- Insira fora da caixa-experiência
- Generalizar
- Encerrar
 
![Janela da ferramenta de preparação do sistema com opções de OOBE, Generalização e Encerramento selecionadas.](./media/unexpected-restart-error-during-vm-boot/3.png)
