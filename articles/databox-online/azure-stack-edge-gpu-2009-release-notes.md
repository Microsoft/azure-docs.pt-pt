---
title: Notas de lançamento do Azure Stack Edge Pro GA Microsoft Docs
description: Descreve questões e resoluções abertas críticas para o Azure Stack Edge Pro que executa o lançamento geral da disponibilidade.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 4aa25024273d62fe5b292d329f6470a828b952a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449530"
---
# <a name="azure-stack-edge-pro-with-gpu-general-availability-ga-release-notes"></a>Azure Stack Edge Pro com notas de lançamento gpu Geral Availability (GA)

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para a libertação geral de disponibilidade (GA) para os seus dispositivos Azure Stack Edge Pro com GPU.

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu dispositivo Azure Stack Edge Pro, reveja cuidadosamente as informações contidas nas notas de lançamento.

Este artigo aplica-se ao lançamento do **Azure Stack Edge Pro 2010** que mapeia para a versão do software número **2.1.1377.2170**.

## <a name="whats-new"></a>Novidades

As seguintes novas funcionalidades estão disponíveis na versão Azure Stack Edge 2010. 

- **Aulas de armazenamento** - Neste lançamento, estão disponíveis aulas de armazenamento que lhe permitem armazenar dinamicamente. Para obter mais informações, consulte a [gestão de armazenamento da Kubernetes no seu dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Painel de persa com servidor de métricas** - Nesta versão, um Painel de Instrumentos Kubernetes é adicionado com um addons servidor de métricas. Pode utilizar o dashboard para obter uma visão geral das aplicações em execução no seu dispositivo Azure Stack Edge Pro, ver o estado dos recursos do cluster Kubernetes e ver quaisquer erros que tenham ocorrido no dispositivo. O servidor Metrics agrega o CPU e o uso da memória através dos recursos de Kubernetes no dispositivo. Para obter mais informações, consulte [o painel de instrumentos Kubernetes para monitorizar o seu dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Azure Arc ativou Kubernetes no Azure Stack Edge Pro** - A partir desta versão, pode implementar cargas de trabalho de aplicação no seu dispositivo Azure Stack Edge Pro via Azure Arc ativado kubernetes. O Azure Arc é uma ferramenta de gestão híbrida que permite implementar aplicações nos seus clusters Kubernetes. Para obter mais informações, consulte [implementar cargas de trabalho através do Arco Azure no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Problemas conhecidos 

A tabela seguinte fornece um resumo de problemas conhecidos para o dispositivo Azure Stack Edge Pro.

| Não. | Funcionalidade | Problema | Soluções/comentários |
| --- | --- | --- | --- |
|**1.**|Funcionalidades de pré-visualização |Para esta versão GA, as seguintes funcionalidades: Local Azure Resource Manager, VMs, Kubernetes, Azure Arc habilitado a Kubernetes, serviço multi-processo (MPS) para GPU - estão todas disponíveis na pré-visualização do seu dispositivo Azure Stack Edge Pro.  |Estas funcionalidades estarão geralmente disponíveis num lançamento posterior. |
| **2.** |Azure Stack Edge Pro + Azure SQL | A criação da base de dados SQL requer acesso ao Administrador.   |Faça os seguintes passos em vez dos passos 1-2 em [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Na UI local do seu dispositivo, ative a interface compute. **Selecione Compute > Port # > Ative para calcular > Apply.**</li><li>Faça o download `sqlcmd` na sua máquina de clientes a partir de https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Ligue-se ao endereço IP da interface de computação (a porta que estava ativada), adicionando um "1401" ao final do endereço.</li><li>O comando final será assim: sqlcmd -S {Interface IP},1401 -U SA -P "Forte! Passw0rd".</li>Depois disso, os passos 3-4 da documentação atual devem ser idênticos. </li></ul> |
| **3.** |Atualizar| Alterações incrementais nas bolhas restauradas via **Refresh** NÃO são suportadas |No caso dos pontos finais blob, as atualizações parciais de blobs após uma Atualização, podem resultar em que as atualizações não sejam carregadas na nuvem. Por exemplo, sequência de ações como:<ul><li>Crie bolha na nuvem. Ou elimine uma bolha previamente carregada do dispositivo.</li><li>Refresque a bolha da nuvem para o aparelho utilizando a funcionalidade de atualização.</li><li>Atualize apenas uma parte da bolha utilizando APIs de REST Azure SDK.</li></ul>Estas ações podem resultar em secções atualizadas da bolha para não ser atualizada na nuvem. <br>**Solução alternativa**: Utilize ferramentas como robocopia, ou cópia de ficheiro regular através do Explorer ou da linha de comando, para substituir bolhas inteiras.|
|**4.**|Limitação|Durante o estrangulamento, se não forem permitidas novas gravações no dispositivo, as gravações feitas pelo cliente NFS falham com o erro "Permissão Negada".| O erro mostrará o seguinte:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: não é possível criar 'teste' de diretório: Permissão negada|
|**5.**|Ingestão de armazenamento blob|Ao utilizar a versão 10 do AzCopy para a ingestão de armazenamento de Blob, execute a AzCopy com o seguinte argumento: `Azcopy <other arguments> --cap-mbps 2000`| Se estes limites não forem fornecidos para a AzCopy, então pode potencialmente enviar um grande número de pedidos para o dispositivo e resultar em problemas com o serviço.|
|**6.**|Contas de armazenamento hierárquico|Aplicam-se as seguintes contas de armazenamento hierárquicos:<ul><li> Apenas bolhas de blocos são suportadas. As bolhas de página não são suportadas.</li><li>Não existe instantâneo ou suporte de cópia da API.</li><li> A ingestão de carga de trabalho de Hadoop `distcp` através não é suportada, uma vez que utiliza fortemente a operação de cópia.</li></ul>||
|**7.**|Ligação de partilha NFS|Se vários processos estiverem a copiar para a mesma parte e o `nolock` atributo não for utilizado, poderá ver erros durante a cópia.|O `nolock` atributo deve ser passado para o comando de montagem para copiar ficheiros para a partilha NFS. Por exemplo: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**8.**|Aglomerado de Kubernetes|Ao aplicar uma atualização no seu dispositivo que esteja a executar um cluster kubernetes, as máquinas virtuais kubernetes reiniciarão e reiniciarão. Neste caso, apenas as cápsulas que são implantadas com réplicas especificadas são automaticamente restauradas após uma atualização.  |Se criou cápsulas individuais fora de um controlador de replicação sem especificar um conjunto de réplicas, então estas cápsulas não serão automaticamente restauradas após a atualização do dispositivo. Vai precisar restaurar estas cápsulas.<br>Um conjunto de réplicas substitui as cápsulas que são eliminadas ou terminadas por qualquer motivo, tais como falha de nó ou atualização de nó disruptivo. Por esta razão, recomendamos que utilize um conjunto de réplicas mesmo que a sua aplicação exija apenas uma única cápsula.|
|**9.**|Aglomerado de Kubernetes|Kubernetes em Azure Stack Edge Pro é suportado apenas com Helm v3 ou mais tarde. Para mais informações, aceda a [perguntas frequentes: Remoção de Tiller](https://v3.helm.sh/docs/faq/).|
|**10.**|Kubernetes ativado pelo Azure Arc |Para a versão GA, a Azure Arc habilitada a Kubernetes é atualizada da versão 0.1.18 para 0.2.9. Uma vez que a atualização de Kubernetes ativada pelo Arco Azure não é suportada no dispositivo Azure Stack Edge, terá de recolocar o Azure Arc ativado por Kubernetes.|Siga estes passos:<ol><li>[Aplicar software de dispositivos e atualizações de Kubernetes](azure-stack-edge-gpu-install-update.md).</li><li>Ligue-se à [interface PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Retire o agente Azure Arc existente. Tipo: `Remove-HcsKubernetesAzureArcAgent` .</li><li>Implementar [o Arco azul para um novo recurso](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Não utilize um recurso Azure Arc existente.</li></ol>|
|**11.**|Kubernetes ativado pelo Azure Arc|As implementações do Azure Arc não são suportadas se o representante web estiver configurado no seu dispositivo Azure Stack Edge Pro.||
|**12.**|Utilizar o Kubernetes |A porta 31000 está reservada para o Painel Kubernetes. Da mesma forma, na configuração padrão, os endereços IP 172.28.0.1 e 172.28.0.10, são reservados para o serviço Kubernetes e o serviço Core DNS, respectivamente.|Não utilize os IPs reservados.|
|**13.**|Utilizar o Kubernetes |A Kubernetes não permite atualmente serviços loadBalancer com vários protocolos. Por exemplo, um serviço DNS que teria de ouvir tanto o TCP como a UDP. |Para contornar esta limitação de Kubernetes com MetalLB, dois serviços (um para TCP, um para UDP) podem ser criados no mesmo seletor de vagem. Estes serviços utilizam a mesma chave de partilha e spec.loadBalancerIP para partilhar o mesmo endereço IP. Os IPs também podem ser partilhados se tiver mais serviços do que endereços IP disponíveis. <br> Para mais informações, consulte [a partilha de endereços IP.](https://metallb.universe.tf/usage/#ip-address-sharing)|
|**14.**|Aglomerado de Kubernetes|Os módulos de mercado Azure IoT Edge existentes podem necessitar de modificações para funcionar no dispositivo IoT Edge no dispositivo Azure Stack Edge.|Para obter mais informações, consulte modificar os módulos Azure IoT Edge do mercado para executar no dispositivo Azure Stack Edge.<!-- insert link-->|
|**15.**|Utilizar o Kubernetes |Os suportes de encaixe baseados em ficheiros não são suportados com Azure IoT Edge em Kubernetes no dispositivo Azure Stack Edge.|IoT Edge usa uma camada de tradução para traduzir `ContainerCreate` opções para construções de Kubernetes. A criação de `Binds` mapas para o diretório de anfitriões e, portanto, os suportes de ligação baseados em ficheiros não podem ser ligados a caminhos em contentores IoT Edge. Se possível, mapear o diretório dos pais.|
|**16.**|Utilizar o Kubernetes |Se trouxer os seus próprios certificados para IoT Edge e os adicionar no seu dispositivo Azure Stack Edge depois de o cálculo estar configurado no dispositivo, os novos certificados não são recolhidos.|Para resolver este problema, deverá fazer o upload dos certificados antes de configurar o cálculo no dispositivo. Se o cálculo já estiver configurado, [ligue-se à interface PowerShell do dispositivo e executar comandos IoT Edge](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Reiniciar `iotedged` e `edgehub` pods.|
|**17.**|Certificados |Em certos casos, o estado de certificação na UI local pode demorar vários segundos a ser atualizado. |Os seguintes cenários na UI local podem ser afetados.<ul><li>**Coluna de estado** na página **de Certificados.**</li><li>**Azulejo** de segurança na página **Get start.**</li><li>Azulejo de **configuração** na página **overview.**</li></ul>  |
|**17.**|IoT Edge |Os módulos implantados através do IoT Edge não podem utilizar a rede de anfitriões. | |
|**18.**|Computação + Kubernetes |Compute/Kubernetes não suporta proxy web NTLM. ||
|**19.**|Compute + web proxy + atualização |Se tiver computado configurado com procuração web, então a atualização do cálculo pode falhar. |Recomendamos que desative o cálculo antes da atualização. |

<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->




## <a name="next-steps"></a>Passos seguintes

- [Prepare-se para implementar o dispositivo Azure Stack Edge Pro com GPU](azure-stack-edge-gpu-deploy-prep.md)