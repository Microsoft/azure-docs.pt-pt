---
title: PERGUNTAS Frequentes sobre desempenho SMB para Azure NetApp Files Microsoft Docs
description: Respostas frequentemente feitas perguntas sobre o desempenho do SMB para ficheiros Azure NetApp.
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
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: 9a07c6ae48cdca68a95db7770d90076eb8f10f95
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929461"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>PERGUNTAS Frequentes sobre desempenho SMB para ficheiros Azure NetApp

Este artigo responde a perguntas frequentes (FAQs) sobre as melhores práticas de desempenho do SMB para ficheiros Azure NetApp.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>O SMB Multicanal está habilitado em ações SMB? 

Sim, o SMB Multicanal está habilitado por padrão, uma mudança implementada no início de janeiro de 2020. Todas as partilhas da SMB pré-datação os volumes SMB existentes tiveram a funcionalidade ativada, e todos os volumes recém-criados também terão a funcionalidade ativada no momento da criação. 

Qualquer ligação SMB estabelecida antes da ativação da funcionalidade terá de ser reiniciada para tirar partido da funcionalidade Multicanal SMB. Para reiniciar, pode desligar e voltar a ligar a parte SMB.

## <a name="is-rss-supported"></a>O RSS está apoiado?

Sim, o Azure NetApp Files suporta a escala lateral de receção (RSS).

Com o SMB Multichannel ativado, um cliente SMB3 estabelece múltiplas ligações TCP ao servidor SMB de Ficheiros Azure NetApp sobre um cartão de interface de rede (NIC) que é capaz de um RSS único. 

## <a name="which-windows-versions-support-smb-multichannel"></a>Quais as versões do Windows que suportam o SMB Multicanal?

O Windows tem suportado o SMB Multichannel desde o Windows 2012 para permitir o melhor desempenho.  Consulte [o ImplementO SMB Multicanal](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) e [os fundamentos do SMB Multicanal](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0) para obter mais detalhes. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>A minha máquina virtual Azure suporta RSS?

Para ver se a sua máquina virtual Azure NICs suporta RSS, executar o comando `Get-SmbClientNetworkInterface` da seguinte forma e verificar o `RSS Capable` campo: 

![Screenshot que mostra saída RSS para máquina virtual Azure.](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Os ficheiros Azure NetApp suportam SMB Direto?

Não, os Ficheiros Azure NetApp não suportam SMB Direct. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>Qual é o benefício do SMB Multicanal? 

A funcionalidade SMB Multichannel permite a um cliente SMB3 estabelecer um conjunto de ligações através de um único cartão de interface de rede (NIC) ou vários NICs e usá-los para enviar pedidos para uma única sessão SMB. Em contrapartida, por design, o SMB1 e o SMB2 exigem que o cliente estabeleça uma ligação e envie todo o tráfego SMB para uma determinada sessão sobre essa ligação. Esta ligação única limita o desempenho global do protocolo que pode ser alcançado a partir de um único cliente.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>Devo configurar vários NICs no meu cliente para a SMB?

N.º O cliente SMB corresponderá à contagem de NIC devolvida pelo servidor SMB.  Cada volume de armazenamento é acessível a partir de um ponto final de armazenamento.  Isso significa que apenas um NIC será usado para qualquer relação SMB.  

Como mostra a saída `Get-SmbClientNetworkInterace` abaixo, a máquina virtual tem 2 interfaces de rede --15 e 12.  Como mostrado sob o seguinte comando `Get-SmbMultichannelConnection` , apesar de existirem dois NICS com capacidade RSS, apenas a interface 12 é utilizada em conexão com a partilha SMB; a interface 15 não está em uso.

![Screeshot que mostra saída para NICS capaz de RSS.](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>A NIC Teaming é apoiada em Azure?

Nic Teaming não é apoiado em Azure. Embora várias interfaces de rede sejam suportadas em máquinas virtuais Azure, elas representam uma construção lógica e não física. Como tal, não oferecem tolerância a falhas.  Além disso, a largura de banda disponível para uma máquina virtual Azure é calculada para a própria máquina e não para qualquer interface de rede individual.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>Como é a performance para o SMB Multichannel?

Os seguintes testes e gráficos demonstram o poder do SMB Multicanal em cargas de trabalho de um só caso.

### <a name="random-io"></a>I/O aleatório  

Com o SMB Multicanal desativado no cliente, foram realizados testes puros de leitura e escrita de 4 KiB utilizando fio e um conjunto de trabalho de 40 GiB.  A quota SMB foi separada entre cada teste, com incrementos da contagem de ligação ao cliente SMB por definições de interface de rede RSS `1` `4` de, `8` , , . `16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` . Os testes mostram que a definição padrão `4` é suficiente para cargas de trabalho intensivas de E/S; incrementando `8` e tendo um efeito `16` negligenciável. 

O comando `netstat -na | findstr 445` provou que foram estabelecidas ligações adicionais com incrementos de `1` e para `4` `8` `16` .  Quatro núcleos de CPU foram totalmente utilizados para sMB durante cada teste, tal como confirmado pela estatística perfmon `Per Processor Network Activity Cycles` (não incluída neste artigo.)

![Gráfico que mostra comparação aleatória de E/O do SMB Multicanal.](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

A máquina virtual Azure não afeta os limites de E/S de armazenamento SMB (nem NFS).  Como mostrado no gráfico seguinte, o tipo de instância D32ds tem uma taxa limitada de 308.000 para iops de armazenamento em cache e 51.200 para IOPS de armazenamento não encatado.  No entanto, o gráfico acima mostra significativamente mais E/S sobre SMB.

![Gráfico que mostra teste de comparação de E/S aleatório.](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>IO sequencial 

Foram efetuados testes semelhantes aos testes de E/S aleatórios descritos anteriormente com I/O sequencial de 64 KiB. Embora os aumentos na contagem de ligação do cliente por interface de rede RSS para além de 4' não tiveram qualquer efeito percetível na I/O aleatória, o mesmo não se aplica à I/O sequencial. Como mostra o gráfico seguinte, cada aumento está associado a um aumento correspondente na produção de leitura. A produção de escrita permaneceu plana devido às restrições de largura de banda de rede colocadas pela Azure para cada instância tipo/tamanho. 

![Gráfico que mostra comparação de testes de produção.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

O Azure coloca limites de taxa de rede em cada tipo/tamanho de máquina virtual. O limite de taxas é imposto apenas ao tráfego de saída. O número de NICs presentes numa máquina virtual não tem qualquer influência na quantidade total de largura de banda disponível para a máquina.  Por exemplo, o tipo de instância D32ds tem um limite de rede imposto de 16.000 Mbps (2.000 MiB/s).  Como mostra o gráfico sequencial acima, o limite afeta o tráfego de saída (escreve) mas não as leituras multicanais.

![Gráfico que mostra teste de comparação sequencial de I/O.](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="what-performance-is-expected-with-a-single-instance-with-a-1-tb-dataset"></a>Que desempenho é esperado com um único caso com um conjunto de dados de 1-TB?

Para fornecer uma visão mais detalhada das cargas de trabalho com misturas de leitura/escrita, os dois gráficos seguintes mostram o desempenho de um único volume de nuvem ultra nível de serviço de 50 TB com um conjunto de dados de 1-TB e com multicanal SMB de 4. Foi utilizado um IODepth ideal de 16, e foram utilizados parâmetros flexíveis IO (FIO) para garantir a utilização integral da largura de banda da rede `numjobs=16` .

O gráfico a seguir mostra os resultados para 4k I/O aleatório, com uma única instância VM e uma mistura de leitura/escrita em intervalos de 10%:

![Gráfico que mostra o padrão do Windows 2019 _D32ds_v4 teste IO aleatório 4K.](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

O gráfico a seguir mostra os resultados da I/O sequencial:

![Gráfico que mostra o padrão do Windows 2019 _D32ds_v4 produção sequencial de 64K.](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="what-performance-is-expected-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>Que desempenho é esperado ao escalonar utilizando 5 VMs com um conjunto de dados de 1-TB?

Estes testes com 5 VMs utilizam o mesmo ambiente de teste que o VM único, com cada processo a escrever para o seu próprio ficheiro.

O gráfico a seguir mostra os resultados para i/O aleatório:

![Gráfico que mostra o padrão do Windows 2019 _D32ds_v4 teste randio IO de 4K de 5 instâncias.](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

O gráfico a seguir mostra os resultados da I/O sequencial:

![Gráfico que mostra o padrão do Windows 2019 _D32ds_v4 produção sequencial de 64K 5 instâncias.](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-do-you-monitor-hyper-v-ethernet-adapters-and-ensure-that-you-maximize-network-capacity"></a>Como monitoriza os adaptadores hiper-V ethernet e assegura que maximiza a capacidade da rede?  

Uma estratégia usada nos testes com a FIO é `numjobs=16` definir. Fazê-lo, cada trabalho é em 16 instâncias específicas para maximizar o Adaptador de Rede Hiper-V da Microsoft.

Pode verificar se há atividade em cada um dos adaptadores no Monitor de Desempenho do Windows selecionando **monitor de desempenho > adicionar contadores > interface de rede > adaptador de rede Microsoft Hyper-V**.

![Screenshot que mostra interface do Contador de Adicionar Monitor de Desempenho.](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

Depois de ter o tráfego de dados a funcionar nos seus volumes, pode monitorizar os seus adaptadores no Windows Performance Monitor. Se não utilizar todos estes 16 adaptadores virtuais, poderá não maximizar a capacidade de largura de banda da sua rede.

![Screenshot que mostra a saída do Monitor de Desempenho.](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="is-accelerated-networking-recommended"></a>Recomenda-se a rede acelerada?

Para um desempenho máximo, recomenda-se que configuure [a rede acelerada sempre](../virtual-network/create-vm-accelerated-networking-powershell.md) que possível. Tenha em mente as seguintes considerações:  

* O portal Azure permite o Networking Acelerado por padrão para máquinas virtuais que suportem esta funcionalidade.  No entanto, outros métodos de implantação, tais como as ferramentas de configuração Ansible e similares, não podem.  A falha na ativação da rede acelerada pode dificultar o desempenho de uma máquina.  
* Se o Networking Acelerado não estiver ativado na interface de rede de uma máquina virtual devido à sua falta de suporte para um tipo ou tamanho de instância, permanecerá desativado com tipos de instância maiores. Vai precisar de intervenção manual nesses casos.

## <a name="are-jumbo-frames-supported"></a>Os quadros jumbo são suportados?

As molduras jumbo não são suportadas com máquinas virtuais Azure.

## <a name="is-smb-signing-supported"></a>A SMB Signing está suportada? 

O protocolo SMB fornece a base para a partilha de ficheiros e impressões e outras operações de networking, como a administração remota do Windows. Para evitar ataques man-in-the-middle que modificam pacotes SMB em trânsito, o protocolo SMB suporta a assinatura digital de pacotes SMB. 

A SMB Signing é suportada para todas as versões de protocolo SMB que são suportadas por Ficheiros Azure NetApp. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>Qual é o impacto de desempenho da SMB Signing?  

A SMB Signing tem um efeito nocivo no desempenho da SMB. Entre outras causas potenciais da degradação do desempenho, a assinatura digital de cada pacote consome CPU adicional do lado do cliente, como mostra a produção perfmon abaixo. Neste caso, o Core 0 aparece responsável pela SMB, incluindo a Assinatura SMB.  Uma comparação com os números de produção de leitura sequencial não multicanal na secção anterior mostra que a assinatura SMB reduz a produção global de 875MiB/s para aproximadamente 250MiB/s. 

![Gráfico que mostra sMB A assinar o impacto do desempenho.](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>Passos seguintes  

- [PERGUNTAS Frequentes Sobre Ficheiros Azure NetApp](azure-netapp-files-faqs.md)
- Consulte os [Ficheiros Azure NetApp: Managed Enterprise File Shares for SMB Workloads](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) about using SMB file shares with Azure NetApp Files.