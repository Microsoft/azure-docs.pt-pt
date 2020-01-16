---
title: Perguntas frequentes sobre o desempenho do SMB para Azure NetApp Files | Microsoft Docs
description: Responde a perguntas frequentes sobre o desempenho do SMB para Azure NetApp Files.
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
ms.date: 01/14/2020
ms.author: b-juche
ms.openlocfilehash: 6391a3eeead6a52371c11011a65f4b4de7260156
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046425"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Perguntas frequentes sobre o desempenho do SMB para Azure NetApp Files

Este artigo responde às perguntas frequentes sobre as práticas recomendadas de desempenho do SMB para Azure NetApp Files.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>O SMB multicanal está habilitado em compartilhamentos SMB? 

Sim, o SMB multicanal está habilitado por padrão, uma alteração foi colocada no início de janeiro de 2020. Todos os compartilhamentos SMB já têm o recurso habilitado e todos os volumes recém-criados também terão o recurso habilitado no momento da criação. 

Qualquer conexão SMB estabelecida antes da habilitação do recurso precisará ser redefinida para tirar proveito da funcionalidade de Fibre Channel do SMB. Para redefinir, você pode desconectar e reconectar o compartilhamento SMB.

## <a name="is-rss-supported"></a>Há suporte para RSS?

Sim, Azure NetApp Files dá suporte ao RSS (recebimento em escala).

Com o SMB multicanal habilitado, um cliente SMB3 estabelece várias conexões TCP com o servidor SMB Azure NetApp Files por meio de uma NIC (placa de interface de rede) com capacidade de RSS único. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Quais versões do Windows oferecem suporte a SMB Multichannel?

O Windows tem suporte para o SMB Multichannel desde o Windows 2012 para permitir o melhor desempenho.  Consulte [implantar o SMB multicanal](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) e [os fundamentos do SMB Multichannel](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) para obter detalhes. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Minha máquina virtual do Azure dá suporte a RSS?

Para ver se suas NICs de máquina virtual do Azure dão suporte a RSS, execute o comando `Get-SmbClientNetworkInterface` da seguinte maneira e verifique o campo `RSS Capable`: 

![Suporte de RSS para máquina virtual do Azure](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp Files dá suporte ao SMB Direct?

Não, o Azure NetApp Files não dá suporte ao SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Qual é o benefício do SMB multicanal? 

O recurso de vários canais SMB permite que um cliente SMB3 estabeleça um pool de conexões em uma única NIC (placa de interface de rede) ou em várias NICs e use-as para enviar solicitações para uma única sessão SMB. Por outro lado, por design, o SMB1 e o SMB2 exigem que o cliente estabeleça uma conexão e envie todo o tráfego SMB para uma determinada sessão por essa conexão. Essa conexão única limita o desempenho geral do protocolo que pode ser obtido de um único cliente.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Devo configurar várias NICs em meu cliente para SMB?

Não. O cliente SMB fará a correspondência da contagem de NIC retornada pelo servidor SMB.  Cada volume de armazenamento pode ser acessado somente de um ponto de extremidade de armazenamento.  Isso significa que apenas uma NIC será usada para qualquer relação SMB específica.  

Como a saída de `Get-SmbClientNetworkInterace` mostrada abaixo, a máquina virtual tem duas interfaces de rede--15 e 12.  Conforme mostrado abaixo na `Get-SmbMultichannelConnection`de comando, mesmo que haja duas NICS compatíveis com RSS, somente a interface 12 é usada em conexão com o compartilhamento SMB; a interface 15 não está em uso.

![NICS compatíveis com RSS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>O agrupamento NIC é compatível com o Azure?

O agrupamento NIC não tem suporte no Azure. Embora haja suporte para várias interfaces de rede em máquinas virtuais do Azure, elas representam uma construção lógica em vez de um constructo físico. Dessa forma, eles não fornecem tolerância a falhas.  Além disso, a largura de banda disponível para uma máquina virtual do Azure é calculada para a própria máquina e não para qualquer interface de rede individual.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Qual é o desempenho como para o Fibre Channel do SMB?

Os seguintes testes e grafos demonstram o poder do SMB multicanal em cargas de trabalho de instância única.

### <a name="random-io"></a>E/s aleatória  

Com o SMB multicanal desabilitado no cliente, testes de leitura e gravação puros de 8 KiB eram executados usando FIO e um conjunto de trabalho 40-GiB.  O compartilhamento SMB foi desanexado entre cada teste, com incrementos da contagem de conexões de cliente SMB por configurações de interface de rede RSS de `1`,`4`,`8`,`16``set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`. Os testes mostram que a configuração padrão de `4` é suficiente para cargas de trabalho com uso intensivo de e/s; incrementar para `8` e `16` não teve efeito. 

O comando `netstat -na | findstr 445` provou que conexões adicionais foram estabelecidas com incrementos de `1` para `4` para `8` e `16`.  Quatro núcleos de CPU foram totalmente utilizados para SMB durante cada teste, conforme confirmado pela estatística de `Per Processor Network Activity Cycles` PerfMon (não incluído neste artigo).

![Testes de e/s aleatórios](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

A máquina virtual do Azure não afeta os limites de e/s de armazenamento SMB (nem NFS).  Como mostrado abaixo, o tipo de instância D16 tem uma taxa limitada de 32.000 para IOPS de armazenamento em cache e 25.600 para IOPS de armazenamento não armazenado em cache.  No entanto, o gráfico acima mostra significativamente mais e/s sobre SMB.

![Comparação de e/s aleatória](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>E/s sequencial 

Testes semelhantes aos testes de e/s aleatórios descritos acima foram executados com e/s sequencial de 64-KiB. Embora o aumento na contagem de conexões de cliente por interface de rede RSS além de 4 ' não tenha nenhum efeito perceptível na e/s aleatória, o mesmo não se aplica à e/s sequencial. Como mostra o gráfico a seguir, cada aumento é associado a um aumento correspondente na taxa de transferência de leitura. A taxa de transferência de gravação permaneceu simples devido a restrições de largura de banda de rede colocadas pelo Azure para cada tipo/tamanho de instância. 

![Testes de e/s sequenciais](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

O Azure coloca limites de taxa de rede em cada tipo/tamanho de máquina virtual. O limite de taxa é imposto somente no tráfego de saída. O número de NICs presentes em uma máquina virtual não tem nenhuma influência sobre a quantidade total de largura de banda disponível para o computador.  Por exemplo, o tipo de instância D16 tem um limite de rede imposto de 8000 Mbps (1.000 MiB/s).  Como mostra o grafo sequencial acima, o limite afeta o tráfego de saída (gravações), mas não as leituras multicanal.

![Comparação de e/s sequencial](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-advanced-networking-recommended"></a>A rede avançada é recomendada?

Para obter o máximo de desempenho, é recomendável configurar a rede avançada sempre que possível. Tenha em mente as seguintes considerações:  

* O portal do Azure habilita a rede avançada por padrão para máquinas virtuais que dão suporte a esse recurso.  No entanto, outros métodos de implantação, como Ansible e ferramentas de configuração semelhantes, podem não.  A falha ao habilitar a rede avançada pode hobble o desempenho de um computador.  
* Se a rede avançada não estiver habilitada na interface de rede de uma máquina virtual devido à sua falta de suporte para um tipo ou tamanho de instância, ela permanecerá desabilitada com tipos de instância maiores. Você precisará de intervenção manual nesses casos.

## <a name="are-jumbo-frames-supported"></a>Há suporte para quadros Jumbo?

Não há suporte para quadros jumbo com máquinas virtuais do Azure.

## <a name="is-smb-signing-supported"></a>Há suporte para assinatura SMB? 

O protocolo SMB fornece a base para o compartilhamento de arquivos e impressão e outras operações de rede, como a administração remota do Windows. Para evitar ataques man-in-the-Middle que modificam pacotes SMB em trânsito, o protocolo SMB dá suporte à assinatura digital de pacotes SMB. 

A assinatura SMB tem suporte para todas as versões de protocolo SMB com suporte pelo Azure NetApp Files. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Qual é o impacto no desempenho da assinatura SMB?  

A assinatura SMB tem um efeito deletério sobre o desempenho do SMB. Entre outras causas potenciais da degradação do desempenho, a assinatura digital de cada pacote consome uma CPU adicional do lado do cliente, conforme mostrado na saída do Perfmon abaixo. Nesse caso, o núcleo 0 é exibido responsável por SMB, incluindo a assinatura SMB.  Uma comparação com os números de taxa de transferência de leitura sequencial não multicanal na seção anterior mostra que a assinatura SMB reduz a taxa de transferência geral de 875MiB/s para aproximadamente 250MiB/s. 

![Impacto no desempenho de assinatura SMB](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Passos seguintes  

- [Perguntas frequentes sobre Azure NetApp Files](azure-netapp-files-faqs.md)
- Consulte o [Azure NetApp Files: compartilhamentos de arquivos corporativos gerenciados para cargas de trabalho SMB](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) sobre como usar compartilhamentos de arquivos smb com Azure NetApp files.