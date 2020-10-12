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
ms.openlocfilehash: 186b1c46303be59e191a1754361e07a2003b997a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87036187"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>Start-up os OS – Computador reiniciou inesperadamente ou encontrou um erro inesperado

Este artigo fornece medidas para resolver problemas em que a máquina virtual (VM) experimenta um inesperado reinício ou erro durante a instalação do Windows.

## <a name="symptom"></a>Sintoma

Quando utilizar [diagnósticos boot](./boot-diagnostics.md) para visualizar a imagem do VM, verá que a imagem mostra a instalação do Windows falhando com o seguinte erro:

**O computador recomeçou inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode prosseguir. Para instalar o Windows, clique em "OK" para reiniciar o computador e, em seguida, reinicie a instalação.**

![Erro enquanto a Instalação do Windows está em andamento: O computador reiniciou inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode prosseguir. Para instalar o Windows, clique em "OK" para reiniciar o computador e, em seguida, reinicie a instalação.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Erro quando a instalação da instalação do Windows está a iniciar serviços: O computador reiniciou inesperadamente ou encontrou um erro inesperado. A instalação do Windows não pode prosseguir. Para instalar o Windows, clique em "OK" para reiniciar o computador e, em seguida, reinicie a instalação.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Causa

A máquina está a tentar fazer uma bota inicial de uma [imagem generalizada](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), mas encontra problemas devido a um ficheiro de resposta personalizado (unattend.xml) a ser processado. Os ficheiros de resposta personalizados não são suportados no Azure. 

O ficheiro de resposta é um ficheiro XML especial que contém definições e valores de definição para as definições de configuração que pretende automatizar durante a instalação de uma instalação do sistema operativo Windows Server. As opções de configuração incluem instruções sobre como dividir discos, onde encontrar a imagem do Windows a ser instalada, chaves do produto a aplicar e outros comandos que gostaria de executar.

No Azure, os ficheiros de resposta personalizados não são suportados. Se especificou um ficheiro **Unattend.xml** personalizado utilizando a `sysprep /unattend:<your file’s name>` opção, este erro pode ocorrer.

Em Azure, utilize a opção **Enter System Out-of-Box Experience (OOBE)** em **Sysprep.exe**, ou utilize em vez do `sysprep /oobe` ficheiro Unattend.xml.

Este problema é criado na maioria das vezes enquanto você está usando **Sysprep.exe** com um VM no local para carregar um VM generalizado para Azure. Nesta situação, você também pode estar interessado em como carregar corretamente um VM generalizado.

## <a name="solution"></a>Solução

### <a name="replace-unattended-answer-file-option"></a>Substitua a opção de ficheiro de resposta não acompanhada

Esta situação ocorre quando uma imagem foi preparada para ser usada em Azure, mas usou um ficheiro de resposta personalizado, que não é suportado em Azure, e você usou **sYSPREP** com uma bandeira semelhante ao seguinte comando:

`sysprep /oobe /generalize /unattend:<NameOfYourAnswerFile.XML> /shutdown`

- No comando anterior, `<NameOfYourAnswerFile.XML>` substitua-o pelo nome do seu ficheiro.

Para corrigir este problema, siga [a orientação do Azure sobre a preparação/captura de uma imagem](../windows/upload-generalized-managed.md) e prepare uma nova imagem generalizada. Durante o sysprep, não utilize `/unattend:<answerfile>` a bandeira. Em vez disso, utilize apenas as bandeiras abaixo:

`sysprep /oobe /generalize /shutdown`

- **Fora da experiência** fora da caixa (OOBE) é a definição suportada para VMs Azure.

Pode também utilizar a **ferramenta de preparação** do sistema GUI para realizar a mesma tarefa que o comando acima, selecionando as opções apresentadas abaixo:

- Insira fora da caixa-experiência
- Generalizar
- Encerrar
 
![Janela da ferramenta de preparação do sistema com opções de OOBE, Generalização e Encerramento selecionadas.](./media/unexpected-restart-error-during-vm-boot/3.png)
