---
title: Notas de lançamento do Azure Stack Edge Pro Preview Microsoft Docs
description: Descreve questões e resoluções abertas críticas para o Azure Stack Edge Pro que executa o lançamento geral da disponibilidade.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/07/2020
ms.author: alkohli
ms.openlocfilehash: d166b0a4c4b69f03d7dba9d997d7d07fbd81ef41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90893975"
---
# <a name="azure-stack-edge-pro-with-gpu-preview-release-notes"></a>Azure Stack Edge Pro com notas de lançamento de pré-visualização da GPU

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para o lançamento de pré-visualização de 2008 para os seus dispositivos Azure Stack Edge Pro com GPU.

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu dispositivo Azure Stack Edge Pro, reveja cuidadosamente as informações contidas nas notas de lançamento.

Este artigo aplica-se ao seguinte lançamento de software - **Azure Stack Edge Pro 2008**. 

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>Novidades

As seguintes novas funcionalidades foram adicionadas no lançamento de Azure Stack Edge 2008. Dependendo da versão específica do software de pré-visualização que está a executar, poderá ver um subconjunto destas funcionalidades. 

- **Aulas de armazenamento** - Na versão anterior, só é possível armazenar de forma estática através de ações SMB ou NFS para aplicações imponentes implantadas no cluster Kubernetes em execução no seu dispositivo Azure Stack Edge Pro. Nesta versão, foram adicionadas aulas de armazenamento que permitem armazenamento dinamicamente. Para obter mais informações, consulte a [gestão de armazenamento da Kubernetes no seu dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Painel de persa com servidor de métricas** - Nesta versão, um Painel de Instrumentos Kubernetes é adicionado com um addons servidor de métricas. Pode utilizar o dashboard para obter uma visão geral das aplicações em execução no seu dispositivo Azure Stack Edge Pro, ver o estado dos recursos do cluster Kubernetes e ver quaisquer erros que tenham ocorrido no dispositivo. O servidor Metrics agrega o CPU e o uso da memória através dos recursos de Kubernetes no dispositivo. Para obter mais informações, consulte [o painel de instrumentos Kubernetes para monitorizar o seu dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Azure Arc for Azure Stack Edge Pro** - A partir desta versão, pode implementar cargas de trabalho de aplicação no seu dispositivo Azure Stack Edge Pro via Azure Arc. O Azure Arc é uma ferramenta de gestão híbrida que permite implementar aplicações nos seus clusters Kubernetes. Para obter mais informações, consulte [implementar cargas de trabalho através do Arco Azure no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Problemas conhecidos 

A tabela seguinte fornece um resumo de problemas conhecidos para o dispositivo Azure Stack Edge Pro.

| N.º | Funcionalidade | Problema | Soluções/comentários |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | A criação da base de dados SQL requer acesso ao Administrador.   |Faça os seguintes passos em vez dos passos 1-2 em [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) . <ul><li>Na UI local do seu dispositivo, ative a interface compute. **Selecione Compute > Port # > Ative para calcular > Apply.**</li><li>Faça o download `sqlcmd` na sua máquina de clientes a partir de https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Ligue-se ao endereço IP da interface de computação (a porta que estava ativada), adicionando um "1401" ao final do endereço.</li><li>O comando final será assim: sqlcmd -S {Interface IP},1401 -U SA -P "Forte! Passw0rd".</li>Depois disso, os passos 3-4 da documentação atual devem ser idênticos. </li></ul> |
| **2.** |Atualizar| Alterações incrementais nas bolhas restauradas via **Refresh** NÃO são suportadas |No caso dos pontos finais blob, as atualizações parciais de blobs após uma Atualização, podem resultar em que as atualizações não sejam carregadas na nuvem. Por exemplo, sequência de ações como:<ul><li>Crie bolha na nuvem. Ou elimine uma bolha previamente carregada do dispositivo.</li><li>Refresque a bolha da nuvem para o aparelho utilizando a funcionalidade de atualização.</li><li>Atualize apenas uma parte da bolha utilizando APIs de REST Azure SDK.</li></ul>Estas ações podem resultar em secções atualizadas da bolha para não ser atualizada na nuvem. <br>**Solução alternativa**: Utilize ferramentas como robocopia, ou cópia de ficheiro regular através do Explorer ou da linha de comando, para substituir bolhas inteiras.|
|**3.**|Limitação|Durante o estrangulamento, se não forem permitidas novas gravações no dispositivo, as gravações feitas pelo cliente NFS falham com o erro "Permissão Negada".| O erro mostrará o seguinte:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: não é possível criar 'teste' de diretório: Permissão negada|
|**4.**|Ingestão de armazenamento blob|Ao utilizar a versão 10 do AzCopy para a ingestão de armazenamento de Blob, execute a AzCopy com o seguinte argumento: `Azcopy <other arguments> --cap-mbps 2000`| Se estes limites não forem fornecidos para a AzCopy, então pode potencialmente enviar um grande número de pedidos para o dispositivo e resultar em problemas com o serviço.|
|**5.**|Contas de armazenamento hierárquico|Aplicam-se as seguintes contas de armazenamento hierárquicos:<ul><li> Apenas bolhas de blocos são suportadas. As bolhas de página não são suportadas.</li><li>Não existe instantâneo ou suporte de cópia da API.</li><li> A ingestão de carga de trabalho de Hadoop `distcp` através não é suportada, uma vez que utiliza fortemente a operação de cópia.</li></ul>||
|**6.**|Ligação de partilha NFS|Se vários processos estiverem a copiar para a mesma parte e o `nolock` atributo não for utilizado, poderá ver erros durante a cópia.|O `nolock` atributo deve ser passado para o comando de montagem para copiar ficheiros para a partilha NFS. Por exemplo: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Aglomerado de Kubernetes|Ao aplicar uma atualização no seu dispositivo que esteja a executar um cluster kubernetes, as máquinas virtuais kubernetes reiniciarão e reiniciarão. Neste caso, apenas as cápsulas que são implantadas com réplicas especificadas são automaticamente restauradas após uma atualização.  |Se criou cápsulas individuais fora de um controlador de replicação sem especificar um conjunto de réplicas, então estas cápsulas não serão automaticamente restauradas após a atualização do dispositivo. Vai precisar restaurar estas cápsulas.<br>Um conjunto de réplicas substitui as cápsulas que são eliminadas ou terminadas por qualquer motivo, tais como falha de nó ou atualização de nó disruptivo. Por esta razão, recomendamos que utilize um conjunto de réplicas mesmo que a sua aplicação exija apenas uma única cápsula.|
|**8.**|Aglomerado de Kubernetes|Kubernetes em Azure Stack Edge Pro é suportado apenas com Helm v3 ou mais tarde. Para mais informações, aceda a [perguntas frequentes: Remoção de Tiller](https://v3.helm.sh/docs/faq/).|
|**9.**|Azure Arc + Azure Stack Edge Pro|As implementações do Azure Arc não são suportadas se o representante web estiver configurado no seu dispositivo Azure Stack Edge Pro.||
|**10.**|Utilizar o Kubernetes |A porta 31000 está reservada para o Painel Kubernetes. Da mesma forma, na configuração padrão, os endereços IP 172.28.0.1 e 172.28.0.10, são reservados para o serviço Kubernetes e o serviço Core DNS, respectivamente.|Não utilize os IPs reservados.|
|**11.**|Utilizar o Kubernetes |A Kubernetes não permite atualmente serviços loadBalancer com vários protocolos. Por exemplo, um serviço DNS que teria de ouvir tanto o TCP como a UDP. |Para contornar esta limitação de Kubernetes com MetalLB, dois serviços (um para TCP, um para UDP) podem ser criados no mesmo seletor de vagem. Estes serviços utilizam a mesma chave de partilha e spec.loadBalancerIP para partilhar o mesmo endereço IP. Os IPs também podem ser partilhados se tiver mais serviços do que endereços IP disponíveis. <br> Para mais informações, consulte [a partilha de endereços IP.](https://metallb.universe.tf/usage/#ip-address-sharing)|
|**12.**|Aglomerado de Kubernetes|Os módulos de marketplace Azure IoT Edge existentes não serão executados no cluster Kubernetes como plataforma de hospedagem para ioT Edge no dispositivo Azure Stack Edge.|Os módulos terão de ser modificados antes de estes serem implantados no dispositivo Azure Stack Edge. Para obter mais informações, consulte modificar os módulos Azure IoT Edge do mercado para executar no dispositivo Azure Stack Edge.<!-- insert link-->|
|**13.**|Utilizar o Kubernetes |Os suportes de encaixe baseados em ficheiros não são suportados com Azure IoT Edge em Kubernetes no dispositivo Azure Stack Edge.|IoT Edge usa uma camada de tradução para traduzir `ContainerCreate` opções para construções de Kubernetes. A criação de `Binds` mapas para o diretório de anfitriões ou para criar e, portanto, os suportes de ligação baseados em ficheiros não podem ser ligados a caminhos em contentores IoT Edge.|
|**14.**|Utilizar o Kubernetes |Se trouxer os seus próprios certificados para IoT Edge e os adicionar no seu dispositivo Azure Stack Edge, os novos certificados não são recolhidos como parte da atualização dos gráficos Helm.|Para resolver este problema, [Ligue-se à interface PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md). Reiniciar `iotedged` e `edgehub` pods.|
|**15.**|Certificados |Em certos casos, o estado de certificação na UI local pode demorar vários segundos a ser atualizado. |Os seguintes cenários na UI local podem ser afetados.<ul><li>**Coluna de estado** na página **de Certificados.**</li><li>**Azulejo** de segurança na página **Get start.**</li><li>Azulejo de **configuração** na página **overview.**</li></ul>  |

## <a name="next-steps"></a>Passos seguintes

- [Prepare-se para implementar o dispositivo Azure Stack Edge Pro com GPU](azure-stack-edge-gpu-deploy-prep.md)

