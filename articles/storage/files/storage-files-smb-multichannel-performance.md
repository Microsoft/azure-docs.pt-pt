---
title: Desempenho multicanal SMB - Ficheiros Azure
description: Saiba mais sobre o desempenho do SMB Multicanal.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 4f4cd8189c9166ee08c1e4ccd800a1202d3b5893
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724821"
---
# <a name="smb-multichannel-performance"></a>Desempenho SMB Multicanal

Azure Files SMB Multichannel (pré-visualização) permite que um cliente SMB 3.x estabeleça múltiplas ligações de rede às ações de ficheiros premium numa conta FileStorage. O protocolo SMB 3.0 introduziu a funcionalidade SMB Multichannel nos clientes Windows Server 2012 e Windows 8. Por isso, qualquer cliente Azure Files SMB 3.x que suporte o SMB Multichannel pode tirar partido da funcionalidade para as suas ações de ficheiros Azure premium. Não existe um custo adicional para permitir o SMB Multichannel numa conta de armazenamento.

## <a name="benefits"></a>Benefícios

A Azure Files SMB Multichannel permite que os clientes utilizem múltiplas ligações de rede que proporcionam um desempenho aumentado, reduzindo o custo de propriedade. O desempenho aumentado é alcançado através da agregação de largura de banda sobre vários NICs e utilizando o suporte de Receive Side Scaling (RSS) para OS NICs para distribuir a carga de IO em vários CPUs.

- **Produção aumentada**: Múltiplas ligações permitem a transferência de dados sobre vários caminhos em paralelo e, assim, beneficia significativamente as cargas de trabalho que utilizam tamanhos de ficheiro maiores com tamanhos de IO maiores, e requerem alta produção de um único VM ou um conjunto menor de VMs. Algumas destas cargas de trabalho incluem meios de comunicação e entretenimento para criação de conteúdos ou transcoding, genómica e análise de risco de serviços financeiros.
- **IOPS superior**: A capacidade de NIC RSS permite uma distribuição eficaz da carga em vários CPUs com múltiplas ligações. Isto ajuda a alcançar uma escala de IOPS mais elevada e uma utilização eficaz dos CPUs VM. Isto é útil para cargas de trabalho com pequenos tamanhos de IO, como aplicações de base de dados.
- **Tolerância à falha da rede**: Múltiplas ligações atenuam o risco de perturbação, uma vez que os clientes já não dependem de uma ligação individual.
- **Configuração automática**: Quando o SMB Multichannel está ativado em clientes e contas de armazenamento, permite a descoberta dinâmica das ligações existentes, e pode criar caminhos de ligação de adição, se necessário.
- **Otimização de custos**: As cargas de trabalho podem atingir uma escala mais elevada a partir de um único VM, ou um pequeno conjunto de VMs, enquanto se conectam a ações premium. Isto poderia reduzir o custo total de propriedade reduzindo o número de VM necessários para executar e gerir uma carga de trabalho.

Para saber mais sobre o SMB Multicanal, consulte a [documentação](/azure-stack/hci/manage/manage-smb-multichannel)do Windows .

Esta funcionalidade proporciona maiores benefícios de desempenho para aplicações multi-roscadas, mas normalmente não ajuda aplicações com fios simples. Consulte a secção [de comparação de desempenho](#performance-comparison) para obter mais detalhes.

## <a name="limitations"></a>Limitações

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Configuração

O SMB Multicanal só funciona quando a funcionalidade está ativada tanto do lado do cliente (o seu cliente) como do lado do serviço (a sua conta de armazenamento Azure).

Nos clientes Windows, o SMB Multichannel é ativado por padrão. Pode verificar a sua configuração executando o seguinte comando PowerShell: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
Na sua conta de armazenamento Azure, terá de ativar o SMB Multichannel. Ver [Ativar o Multicanal SMB (pré-visualização)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>Desativar o Multicanal SMB
Na maioria dos cenários, particularmente cargas de trabalho multi-roscadas, os clientes devem ver um desempenho melhorado com o SMB Multichannel. No entanto, alguns cenários específicos, como cargas de trabalho de linha única ou para efeitos de teste, é possível que pretenda desativar o SMB Multichannel. Consulte [a comparação de desempenho](#performance-comparison) para mais detalhes.

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Verifique se o Multicanal SMB está configurado corretamente

1. Crie uma partilha de ficheiros premium ou use uma existente.
1. Certifique-se de que o seu cliente suporta o SMB Multichannel (um ou mais adaptadores de rede tem o dimensionamento lateral ativado). Consulte a [documentação](/azure-stack/hci/manage/manage-smb-multichannel) do Windows para mais detalhes.
1. Monte uma partilha de arquivo para o seu cliente.
1. Gere carga com a sua aplicação.
    Uma ferramenta de cópia, como robocopia /MT, ou qualquer ferramenta de desempenho como Diskspd para ler/escrever ficheiros pode gerar carga.
1. Open PowerShell como administrador e use o seguinte comando: `Get-SmbMultichannelConnection |fl`
1. Procure propriedades **maxChannels** e **CurrentChannels**

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Screenshot dos resultados Get-SMBMultichannelConnection." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Comparação de desempenho

Existem duas categorias de padrões de carga de trabalho de leitura/escrita - roscados e multi roscados. A maioria das cargas de trabalho usam vários ficheiros, mas pode haver casos específicos de uso em que a carga de trabalho funciona com um único ficheiro numa ação. Esta secção abrange diferentes casos de utilização e o impacto de desempenho para cada um deles. Em geral, a maioria das cargas de trabalho são multi-roscadas e distribuem carga de trabalho por vários ficheiros, pelo que devem observar melhorias significativas de desempenho com o SMB Multichannel.

- **Vários ficheiros roscados/múltiplos**: Dependendo do padrão de carga de trabalho, deverá ver melhoria significativa do desempenho na leitura e escrever IOs em vários canais. Os ganhos de desempenho variam entre 2x e 4x em termos de IOPS, produção e latência. Para esta categoria, o SMB Multichannel deve ser habilitado para o melhor desempenho.
- **Ficheiro multi roscado/único**: Para a maioria dos casos de utilização nesta categoria, as cargas de trabalho beneficiarão de ter o SMB Multichannel ativado, especialmente se a carga de trabalho tiver um tamanho médio de IO > ~16k. Alguns cenários de exemplo que beneficiam do SMB Multichannel são a cópia de segurança ou a recuperação de um único ficheiro grande. Uma exceção em que você pode querer desativar o SMB Multichannel é se a sua carga de trabalho é pequena iOs pesado. Nesse caso, pode observar uma ligeira perda de desempenho de ~10%. Dependendo do caso de utilização, considere espalhar a carga por vários ficheiros ou desativar a funcionalidade. Consulte a secção [de Configuração](#configuration) para mais detalhes.
- **Ficheiros simples/múltiplos ou ficheiro único**: Para a maioria das cargas de trabalho de rosca única, existem benefícios mínimos de desempenho devido à falta de paralelismo, normalmente há uma ligeira degradação do desempenho de ~10% se o SMB Multichannel estiver ativado. Neste caso, é ideal para desativar o SMB Multicanal, com uma exceção. Se a carga de trabalho de rosca única pode distribuir carga em vários ficheiros e utiliza um tamanho IO médio maior (> ~16k), então deve haver pequenos benefícios de desempenho do SMB Multichannel.

### <a name="performance-test-configuration"></a>Configuração do teste de desempenho

Para as tabelas deste artigo, foi utilizada a seguinte configuração: Um único Standard D32s v3 VM com um único RSS ativado NIC com quatro canais. A carga foi gerada usando diskspd.exe, com múltiplas roscas com profundidade de IO de 10, e IOs aleatórios com vários tamanhos de IO.

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Produção de armazenamento em cache máximo e temporário: IOPS/MBps (tamanho da cache em GiB) | Produção de disco não-abacatado por maxilar: IOPS/MBps | NICs máximos|Largura de banda de rede esperada (Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="Screenshot que mostra a configuração do teste de desempenho." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Ficheiros mutli-threaded/múltiplos com SMB Multicanal

A carga foi gerada contra 10 ficheiros com vários tamanhos de IO. Os resultados dos testes de escala mostraram melhorias significativas tanto no IOPS como nos resultados dos testes de produção com o SMB Multichannel ativado. Os seguintes diagramas retratam os resultados:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Diagrama de desempenho" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Diagrama de desempenho de produção." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- Num único NIC, para leituras, observou-se um aumento de desempenho de 2x-3x e, para as escritas, ganhos de 3x-4x em termos de IOPS e de produção.
- O SMB Multicanal permitiu que o IOPS e a produção atingissem os limites de VM mesmo com um único NIC e o limite de quatro canais.
- Uma vez que a saída (ou leituras para armazenamento) não é medido, a produção de leitura foi capaz de exceder o limite de VM publicado de 16.000 Mbps (2 GiB/s). O teste obteve >2,7 GiB/s. A entrada (ou escreve para armazenamento) ainda está sujeita aos limites de VM.
- Espalhar carga sobre vários ficheiros permitiu melhorias substanciais.

Um comando de exemplo que foi usado neste teste é: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Cargas de carga de ficheiros multi roscadas/individuais com o Multicanal SMB

A carga foi gerada contra um único ficheiro GiB de 128. Com o SMB Multichannel ativado, o teste de escala com ficheiros multi-roscados/individuais mostrou melhorias na maioria dos casos. Os seguintes diagramas retratam os resultados:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="Diagrama de desempenho do IOPS." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Diagrama de desempenho de produção de ficheiro único." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- Num único NIC com maior tamanho médio de IO (> ~16k), houve melhorias significativas tanto nas leituras como nas escritas.
- Para tamanhos de IO mais pequenos, houve um ligeiro impacto de ~10% no desempenho quando o SMB Multichannel foi ativado. Isto pode ser atenuado espalhando a carga por vários ficheiros ou desativando a funcionalidade.
- O desempenho ainda está vinculado por  [limites de ficheiro único](storage-files-scale-targets.md#file-level-limits).

## <a name="optimizing-performance"></a>Otimizar o desempenho

As seguintes dicas podem ajudá-lo a otimizar o seu desempenho:

- Certifique-se de que a sua conta de armazenamento e o seu cliente estão na mesma região de Azure para reduzir a latência da rede.
- Utilize aplicações com vários fios e espalhe a carga em vários ficheiros.
- Os benefícios de desempenho do SMB Multichannel aumentam com o número de ficheiros distribuindo carga.
- O desempenho das ações premium está vinculado pelo tamanho das ações previstas (IOPS/egress/ingress) e pelos limites de ficheiros únicos. Para mais informações, consulte [a compreensão das ações de ficheiros premium](understanding-billing.md#provisioned-billing).
- O desempenho máximo de um único cliente VM ainda está vinculado aos limites de VM. Por exemplo, [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) pode suportar uma largura de banda máxima de 16.000 MBps (ou 2GBps), a saída do VM (escreve para armazenamento) é medido, a entrada (lê-se no armazenamento) não é. O desempenho da partilha de ficheiros está sujeito a limites de rede de máquinas, CPUs, armazenamento interno disponível largura de banda de rede, tamanhos de IO, paralelismo, bem como outros fatores.
- O teste inicial é geralmente um aquecimento, deita fora os seus resultados e repete o teste.
- Se o desempenho for limitado por um único cliente e a carga de trabalho ainda estiver abaixo dos limites de ações previstos, um desempenho mais elevado pode ser alcançado espalhando carga sobre vários clientes.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>A relação entre iops, produção e tamanhos IO

**Produção = tamanho IO * IOPS**

Os tamanhos de IO mais elevados conduzem a uma produção mais elevada e terão latências mais elevadas, resultando num menor número de IOPS líquidos. Tamanhos de IO mais pequenos conduzirão iops mais altos, mas resulta em menor produção líquida e latências.

## <a name="next-steps"></a>Passos seguintes

- [Ativar o SMB Multicanal numa conta de arquiteta (pré-visualização)](storage-files-enable-smb-multichannel.md)
- Consulte a [documentação](/azure-stack/hci/manage/manage-smb-multichannel) do Windows para saber mais sobre o SMB Multicanal.
