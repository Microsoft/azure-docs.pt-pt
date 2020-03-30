---
title: FAQs sobre desempenho de SMB para Ficheiros Azure NetApp Microsoft Docs
description: Respostas frequentemente colocadas perguntas sobre o desempenho de SMB para Ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 24b3710861f0ee158619ae9103584dcdb181f3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460454"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>FAQs sobre desempenho sMB para Ficheiros Azure NetApp

Este artigo responde a perguntas frequentes (PERGUNTAS) sobre as melhores práticas de desempenho do SMB para ficheiros Azure NetApp.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>O SMB Multichannel está ativado em ações SMB? 

Sim, o SMB Multichannel está ativado por padrão, uma alteração implementada no início de janeiro de 2020. Todos os volumes SMB existentes pré-datados já tinham a funcionalidade ativada, e todos os volumes recém-criados também terão a funcionalidade ativada no momento da criação. 

Qualquer ligação SMB estabelecida antes da ativação da funcionalidade terá de ser reativada para tirar partido da funcionalidade SMB Multichannel. Para redefinir, pode desligar e reconectar a parte SMB.

## <a name="is-rss-supported"></a>O RSS é apoiado?

Sim, o Azure NetApp Files suporta a escala ção lateral (RSS).

Com o SMB Multichannel ativado, um cliente SMB3 estabelece múltiplas ligações TCP ao servidor SMB do Azure NetApp Files através de um cartão de interface de rede (NIC) que é único capaz de RSS. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Quais as versões do Windows suportam o SMB Multichannel?

O Windows tem suportado o SMB Multichannel desde o Windows 2012 para permitir o melhor desempenho.  Consulte [o Multicanal SMB](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) e [os fundamentos do Multicanal SMB](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) para mais detalhes. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>A minha máquina virtual Azure suporta RSS?

Para ver se os NICs da sua máquina virtual `Get-SmbClientNetworkInterface` Azure suportam `RSS Capable`RSS, execute o comando da seguinte forma e verifique o campo: 

![Suporte RSS para máquina virtual Azure](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Os Ficheiros Azure NetApp suportam o SMB Direct?

Não, o Azure NetApp Files não suporta o SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Qual é o benefício do Multicanal SMB? 

A funcionalidade SMB Multichannel permite a um cliente SMB3 estabelecer um conjunto de ligações através de um único cartão de interface de rede (NIC) ou de vários NICs e usá-los para enviar pedidos para uma única sessão de SMB. Em contrapartida, por design, sMB1 e SMB2 exigem que o cliente estabeleça uma ligação e envie todo o tráfego SMB para uma determinada sessão sobre essa ligação. Esta única ligação limita o desempenho global do protocolo que pode ser alcançado a partir de um único cliente.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Devo configurar vários NICs no meu cliente para SMB?

Não. O cliente SMB corresponderá à contagem nic devolvida pelo servidor SMB.  Cada volume de armazenamento é acessível a partir de um ponto final de armazenamento.  Isso significa que apenas um NIC será usado para qualquer relação SMB.  

Como mostra `Get-SmbClientNetworkInterace` a saída abaixo, a máquina virtual tem duas interfaces de rede : 15 e 12.  Como mostrado abaixo sob `Get-SmbMultichannelConnection`o comando , embora existam dois NICS com capacidade RSS, apenas a interface 12 é utilizada em conexão com a parte SMB; interface 15 não está a ser utilizado.

![NICS com capacidade RSS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>Nic Teaming é apoiado em Azure?

Nic Teaming não é suportado em Azure. Embora várias interfaces de rede sejam suportadas em máquinas virtuais Azure, representam uma construção lógica e não física. Como tal, não oferecem tolerância a falhas.  Além disso, a largura de banda disponível para uma máquina virtual Azure é calculada para a própria máquina e não para qualquer interface de rede individual.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Como é a performance para o SMB Multichannel?

Os seguintes testes e gráficos demonstram a potência do SMB Multichannel em cargas de trabalho de instância única.

### <a name="random-io"></a>I/O aleatório  

Com o SMB Multichannel desativado no cliente, foram realizados testes puros de leitura e escrita de 8-KiB utilizando o FIO e um conjunto de trabalho de 40 GiB.  A quota SMB foi separada entre cada teste, com incrementos da contagem de `1``4`ligação ao cliente SMB por definições de interface de rede RSS`8`de, , ,`16`, `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`. Os ensaios mostram que `4` a definição padrão de cargas de trabalho intensivas em I/O; incrementando `8` `16` e não teve efeito. 

O `netstat -na | findstr 445` comando provou que foram estabelecidas `1` ligações adicionais com incrementos de e `4` `8` para `16`.  Quatro núcleos cpu foram totalmente utilizados para SMB durante `Per Processor Network Activity Cycles` cada teste, como confirmado pela estatística de perfmon (não incluído neste artigo.)

![Testes de I/O aleatórios](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

A máquina virtual Azure não afeta os limites de armazenamento De SMB (nem NFS).  Como mostrado abaixo, o tipo de instância D16 tem uma taxa limitada de 32.000 para iOPS de armazenamento em cache e 25.600 para IOPS de armazenamento não cached.  No entanto, o gráfico acima mostra significativamente mais I/O sobre SMB.

![Comparação aleatória de I/S](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>IO sequencial 

Foram efetuados testes semelhantes aos testes de I/O aleatórios acima descritos com I/O sequencial de 64-KiB. Embora os aumentos na contagem de ligação ao cliente por interface de rede RSS para além de 4' não tenham tido um efeito notável em I/O aleatório, o mesmo não se aplica ao I/O sequencial. Como mostra o gráfico seguinte, cada aumento está associado a um aumento correspondente na entrada de leitura. A entrada de escrita permaneceu plana devido às restrições de largura de banda da rede colocadas pelo Azure para cada instância tipo/tamanho. 

![Testes sequenciais de I/S](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

O Azure coloca limites de taxa de rede em cada tipo/tamanho da máquina virtual. O limite de taxas é imposto apenas ao tráfego de saída. O número de NICs presentes numa máquina virtual não tem qualquer influência sobre a quantidade total de largura de banda disponível para a máquina.  Por exemplo, o tipo de instância D16 tem um limite de rede imposto de 8000 Mbps (1.000 MiB/s).  Como mostra o gráfico sequencial acima, o limite afeta o tráfego de saída (escreve) mas não o multicanal.

![Comparação sequencial de I/S](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>Recomenda-se a Ligação Acelerada?

Para um desempenho máximo, recomenda-se que configure a [Rede Acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) sempre que possível. Tenha em mente as seguintes considerações:  

* O portal Azure permite o Networking Acelerado por padrão para máquinas virtuais que suportam esta funcionalidade.  No entanto, outros métodos de implementação, tais como ferramentas de configuração ansíveis e similares, podem não.  A não ativação da rede acelerada pode dificultar o desempenho de uma máquina.  
* Se a rede acelerada não estiver ativada na interface de rede de uma máquina virtual devido à sua falta de suporte para um tipo ou tamanho de instância, permanecerá desativada com tipos de instâncias maiores. Nesses casos, vai precisar de intervenção manual.

## <a name="are-jumbo-frames-supported"></a>Os quadros jumbo são suportados?

As molduras jumbo não são suportadas com máquinas virtuais Azure.

## <a name="is-smb-signing-supported"></a>A Assinatura SMB é suportada? 

O protocolo SMB fornece a base para a partilha de ficheiros e impressões e outras operações de networking, tais como a administração remota do Windows. Para evitar ataques man-in-the-middle que modificam pacotes SMB em trânsito, o protocolo SMB suporta a assinatura digital de pacotes SMB. 

A SMB Signing é suportada para todas as versões protocolares SMB que são suportadas por Ficheiros Azure NetApp. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Qual é o impacto do desempenho da SMB Signing?  

A SMB Signing tem um efeito prejudicial no desempenho do SMB. Entre outras causas potenciais da degradação do desempenho, a assinatura digital de cada pacote consome CPU adicional do lado do cliente, como mostra a saída de perfmon abaixo. Neste caso, o Core 0 aparece responsável pela SMB, incluindo a SMB Signing.  Uma comparação com os números de entrada sequenciais não multicanal na secção anterior mostra que a Assinatura SMB reduz a entrada global de 875MiB/s para aproximadamente 250MiB/s. 

![Impacto de desempenho da Assinatura SMB](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Passos seguintes  

- [PERGUNTAS FREQUENTES Sobre ficheiros Azure NetApp](azure-netapp-files-faqs.md)
- Consulte os [Ficheiros Azure NetApp: Managed Enterprise File Shares for SMB Workloads](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) about using SMB file shares with Azure NetApp Files.
