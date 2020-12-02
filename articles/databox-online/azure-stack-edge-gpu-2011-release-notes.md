---
title: Notas de lançamento do Azure Stack Edge Pro 2011
description: Descreve questões e resoluções abertas críticas para o lançamento de Azure Stack Edge Pro em execução em 2011.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/24/2020
ms.author: alkohli
ms.openlocfilehash: 158e3cc17c146c5e05959d2faf7d75c9bcc12dcb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467229"
---
# <a name="azure-stack-edge-2011-release-notes"></a>Notas de lançamento do Azure Stack Edge 2011

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para o lançamento de 2011 para os seus dispositivos Azure Stack Edge. Estas notas de lançamento são aplicáveis para os dispositivos Azure Stack Edge Pro GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R. Funcionalidades e problemas que correspondam a um modelo específico são chamados onde for aplicável.

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu dispositivo, reveja cuidadosamente as informações contidas nas notas de lançamento.

Este artigo aplica-se ao lançamento do **Azure Stack Edge 2011**<!--which maps to software version number **2.1.XXXX.XXXX**-->.

## <a name="whats-new"></a>Novidades

As seguintes novas funcionalidades estão disponíveis no lançamento do Azure Stack Edge 2011. 

- **Disponibilidade geral de dispositivos Azure Stack Edge Pro R e Azure Stack Edge Mini R** - A partir desta versão, estarão disponíveis os dispositivos Azure Stack Edge Pro R e Azure Stack Edge Mini R. Para mais informações, consulte [O que é Azure Stack Edge Pro R](azure-stack-edge-j-series-overview.md) e What is [Azure Stack Edge Mini R](azure-stack-edge-k-series-overview.md).  
- **Gestão em nuvem de Máquinas Virtuais** - A partir desta versão, pode criar e gerir as máquinas virtuais do seu dispositivo através do portal Azure. Para mais informações, consulte [implementar VMs através do portal Azure.](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- **Integração com o Azure Monitor** - Pode agora utilizar o Azure Monitor para monitorizar os contentores a partir das aplicações de computação que funcionam no seu dispositivo. A loja de métricas Azure Monitor não é suportada nesta versão. Para obter mais informações, consulte como [ativar o Azure Monitor no seu dispositivo](azure-stack-edge-gpu-enable-azure-monitor.md).
- **Registo do contentor edge** - Nesta versão, está disponível um registo de contentores Edge que fornece um repositório na borda do seu dispositivo. Pode utilizar este registo para armazenar e gerir imagens de contentores. Para obter mais informações, consulte [o registo do contentor Enable Edge](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). 
- **Rede Privada Virtual (VPN)** - Utilize a VPN para fornecer outra camada de encriptação para os dados que fluem entre os dispositivos e a nuvem. A VPN está disponível apenas no Azure Stack Edge Pro R e no Azure Stack Edge Mini R. Para obter mais informações, consulte como [configurar a VPN no seu dispositivo](azure-stack-edge-mini-r-configure-vpn-powershell.md). 
- **Rode as teclas de encriptação no repouso** - Poderá agora rodar as teclas de encriptação em repouso que são utilizadas para proteger as unidades do seu dispositivo. Esta funcionalidade está disponível apenas para dispositivos Azure Stack Edge Pro R e Azure Stack Edge Mini R. Para obter mais informações, consulte [as teclas de encriptação em repouso rotativa](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#manage-access-to-device-data).
- **Registo proactivo** - A partir desta versão, pode permitir a recolha proativa de registos no seu dispositivo com base nos indicadores de saúde do sistema para ajudar a resolver eficazmente quaisquer problemas do dispositivo. Para obter mais informações, consulte [a recolha de registos Proactive no seu dispositivo](azure-stack-edge-gpu-proactive-log-collection.md).


## <a name="known-issues-in-2011-release"></a>Questões conhecidas no lançamento de 2011

A tabela seguinte fornece um resumo de questões conhecidas no lançamento de 2011.

| Não. | Funcionalidade | Problema | Soluções/comentários |
| --- | --- | --- | --- |
|**1.**|Funcionalidades de pré-visualização |Para esta versão, as seguintes funcionalidades: Local Azure Resource Manager, VMs, Gestão em nuvem de VMs, Azure Arc habilitado Kubernetes, VPN para Azure Stack Edge Pro R e Azure Stack Edge Mini R, serviço multi-processo (MPS) para GPU Azure Stack Edge Pro - estão todos disponíveis na pré-visualização.  |Estas funcionalidades estarão geralmente disponíveis em lançamentos posteriores. |
|**2.**|Painel de Kubernetes | O ponto final *https* para o Painel Kubernetes com certificado SSL não é suportado. | |
|**3.**|Utilizar o Kubernetes |O registo do contentor de borda não funciona quando o proxy web está ativado.|A funcionalidade estará disponível num futuro lançamento. |
|**4.**|Utilizar o Kubernetes |O registo do contentor de borda não funciona com módulos IoT Edge.| |
|**5.**|Utilizar o Kubernetes |Kubernetes não suporta " em nomes variáveis ambientais que são usados por aplicações .NET. Isto também é necessário para que o módulo IoT Edge da grelha de evento funcione no dispositivo Azure Stack Edge e outras aplicações. Para mais informações, consulte [ASP.NET documentação principal.](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1#environment-variables)|Substitua " por duplo sublinhado. Para mais informações,consulte [a edição de Kubernetes](https://github.com/kubernetes/kubernetes/issues/53201)|
|**6.** |Aglomerado Azure Arc + Kubernetes |Por predefinição, quando os recursos `yamls` são eliminados do repositório git, os recursos correspondentes não são eliminados do cluster Kubernetes.  |É necessário definir `--sync-garbage-collection`  no Arc OperatorParams para permitir a supressão de recursos quando eliminado do repositório de git. Para obter mais informações, consulte [Eliminar uma configuração](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters). |
|**7.**|NFS |As aplicações que utilizam suportes de partilha NFS no seu dispositivo para escrever dados devem utilizar a escrita exclusiva. Isto garante que as escritas são escritas para o disco.| |
|**8.**|Configuração do cálculo |A configuração do cálculo falha nas configurações de rede onde gateways ou switches ou routers respondem aos pedidos do Address Resolution Protocol (ARP) para sistemas que não existem na rede.| |
|**9.**|Computação e Kubernetes |Se a Kubernetes for configurada primeiro no seu dispositivo, reclama todas as GPUs disponíveis. Assim, não é possível criar VMs Azure Resource Manager usando GPUs após a configuração dos Kubernetes. |Se o seu dispositivo tiver 2 GPUs, então pode criar 1 VM que usa a GPU e, em seguida, configurar Kubernetes. Neste caso, a Kubernetes utilizará os restantes 1 GPU disponíveis. |


## <a name="known-issues-from-previous-releases"></a>Questões conhecidas de lançamentos anteriores 

O quadro seguinte fornece um resumo das questões conhecidas transitadas das versões anteriores.

| Não. | Funcionalidade | Problema | Soluções/comentários |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | A criação da base de dados SQL requer acesso ao Administrador.   |Faça os seguintes passos em vez dos passos 1-2 em [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) . <ul><li>Na UI local do seu dispositivo, ative a interface compute. **Selecione Compute > Port # > Ative para calcular > Apply.**</li><li>Faça o download `sqlcmd` na sua máquina de clientes a partir de https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Ligue-se ao endereço IP da interface de computação (a porta que estava ativada), adicionando um "1401" ao final do endereço.</li><li>O comando final será assim: sqlcmd -S {Interface IP},1401 -U SA -P "Forte! Passw0rd".</li>Depois disso, os passos 3-4 da documentação atual devem ser idênticos. </li></ul> |
| **2.** |Atualizar| Alterações incrementais nas bolhas restauradas via **Refresh** NÃO são suportadas |No caso dos pontos finais blob, as atualizações parciais de blobs após uma Atualização, podem resultar em que as atualizações não sejam carregadas na nuvem. Por exemplo, sequência de ações como:<ul><li>Crie bolha na nuvem. Ou elimine uma bolha previamente carregada do dispositivo.</li><li>Refresque a bolha da nuvem para o aparelho utilizando a funcionalidade de atualização.</li><li>Atualize apenas uma parte da bolha utilizando APIs de REST Azure SDK.</li></ul>Estas ações podem resultar em secções atualizadas da bolha para não ser atualizada na nuvem. <br>**Solução alternativa**: Utilize ferramentas como robocopia, ou cópia de ficheiro regular através do Explorer ou da linha de comando, para substituir bolhas inteiras.|
|**3.**|Limitação|Durante o estrangulamento, se não forem permitidas novas gravações no dispositivo, as gravações feitas pelo cliente NFS falham com o erro "Permissão Negada".| O erro mostrará o seguinte:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: não é possível criar 'teste' de diretório: Permissão negada|
|**4.**|Ingestão de armazenamento blob|Ao utilizar a versão 10 do AzCopy para a ingestão de armazenamento de Blob, execute a AzCopy com o seguinte argumento: `Azcopy <other arguments> --cap-mbps 2000`| Se estes limites não forem fornecidos para a AzCopy, então pode potencialmente enviar um grande número de pedidos para o dispositivo e resultar em problemas com o serviço.|
|**5.**|Contas de armazenamento hierárquico|Aplicam-se as seguintes contas de armazenamento hierárquicos:<ul><li> Apenas bolhas de blocos são suportadas. As bolhas de página não são suportadas.</li><li>Não existe instantâneo ou suporte de cópia da API.</li><li> A ingestão de carga de trabalho de Hadoop `distcp` através não é suportada, uma vez que utiliza fortemente a operação de cópia.</li></ul>||
|**6.**|Ligação de partilha NFS|Se vários processos estiverem a copiar para a mesma parte e o `nolock` atributo não for utilizado, poderá ver erros durante a cópia.|O `nolock` atributo deve ser passado para o comando de montagem para copiar ficheiros para a partilha NFS. Por exemplo: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Aglomerado de Kubernetes|Ao aplicar uma atualização no seu dispositivo que esteja a executar um cluster kubernetes, as máquinas virtuais kubernetes reiniciarão e reiniciarão. Neste caso, apenas as cápsulas que são implantadas com réplicas especificadas são automaticamente restauradas após uma atualização.  |Se criou cápsulas individuais fora de um controlador de replicação sem especificar um conjunto de réplicas, então estas cápsulas não serão automaticamente restauradas após a atualização do dispositivo. Vai precisar restaurar estas cápsulas.<br>Um conjunto de réplicas substitui as cápsulas que são eliminadas ou terminadas por qualquer motivo, tais como falha de nó ou atualização de nó disruptivo. Por esta razão, recomendamos que utilize um conjunto de réplicas mesmo que a sua aplicação exija apenas uma única cápsula.|
|**8.**|Aglomerado de Kubernetes|Kubernetes em Azure Stack Edge Pro é suportado apenas com Helm v3 ou mais tarde. Para mais informações, aceda a [perguntas frequentes: Remoção de Tiller](https://v3.helm.sh/docs/faq/).|
|**9.**|Kubernetes ativado pelo Azure Arc |Para a versão GA, a Azure Arc habilitada a Kubernetes é atualizada da versão 0.1.18 para 0.2.9. Uma vez que a atualização de Kubernetes ativada pelo Arco Azure não é suportada no dispositivo Azure Stack Edge, terá de recolocar o Azure Arc ativado por Kubernetes.|Siga estes passos:<ol><li>[Aplicar software de dispositivos e atualizações de Kubernetes](azure-stack-edge-gpu-install-update.md).</li><li>Ligue-se à [interface PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Retire o agente Azure Arc existente. Tipo: `Remove-HcsKubernetesAzureArcAgent` .</li><li>Implementar [o Arco azul para um novo recurso](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Não utilize um recurso Azure Arc existente.</li></ol>|
|**10.**|Kubernetes ativado pelo Azure Arc|As implementações do Azure Arc não são suportadas se o representante web estiver configurado no seu dispositivo Azure Stack Edge Pro.||
|**11.**|Utilizar o Kubernetes |A porta 31000 está reservada para o Painel Kubernetes. A porta 31001 está reservada para o registo do contentor Edge. Da mesma forma, na configuração padrão, os endereços IP 172.28.0.1 e 172.28.0.10, são reservados para o serviço Kubernetes e o serviço Core DNS, respectivamente.|Não utilize os IPs reservados.|
|**12.**|Utilizar o Kubernetes |A Kubernetes não permite atualmente serviços loadBalancer com vários protocolos. Por exemplo, um serviço DNS que teria de ouvir tanto o TCP como a UDP. |Para contornar esta limitação de Kubernetes com MetalLB, dois serviços (um para TCP, um para UDP) podem ser criados no mesmo seletor de vagem. Estes serviços utilizam a mesma chave de partilha e spec.loadBalancerIP para partilhar o mesmo endereço IP. Os IPs também podem ser partilhados se tiver mais serviços do que endereços IP disponíveis. <br> Para mais informações, consulte [a partilha de endereços IP.](https://metallb.universe.tf/usage/#ip-address-sharing)|
|**13.**|Aglomerado de Kubernetes|Os módulos de mercado Azure IoT Edge existentes podem necessitar de modificações para funcionar no dispositivo IoT Edge no dispositivo Azure Stack Edge.|Para obter mais informações, consulte modificar os módulos Azure IoT Edge do mercado para executar no dispositivo Azure Stack Edge.<!-- insert link-->|
|**14.**|Utilizar o Kubernetes |Os suportes de encaixe baseados em ficheiros não são suportados com Azure IoT Edge em Kubernetes no dispositivo Azure Stack Edge.|IoT Edge usa uma camada de tradução para traduzir `ContainerCreate` opções para construções de Kubernetes. A criação de `Binds` mapas para o diretório de anfitriões e, portanto, os suportes de ligação baseados em ficheiros não podem ser ligados a caminhos em contentores IoT Edge. Se possível, mapear o diretório dos pais.|
|**15.**|Utilizar o Kubernetes |Se trouxer os seus próprios certificados para IoT Edge e os adicionar no seu dispositivo Azure Stack Edge depois de o cálculo estar configurado no dispositivo, os novos certificados não são recolhidos.|Para resolver este problema, deverá fazer o upload dos certificados antes de configurar o cálculo no dispositivo. Se o cálculo já estiver configurado, [ligue-se à interface PowerShell do dispositivo e executar comandos IoT Edge](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Reiniciar `iotedged` e `edgehub` pods.|
|**16.**|Certificados |Em certos casos, o estado de certificação na UI local pode demorar vários segundos a ser atualizado. |Os seguintes cenários na UI local podem ser afetados.<ul><li>**Coluna de estado** na página **de Certificados.**</li><li>**Azulejo** de segurança na página **Get start.**</li><li>Azulejo de **configuração** na página **overview.**</li></ul>  |
|**17.**|IoT Edge |Os módulos implantados através do IoT Edge não podem utilizar a rede de anfitriões. | |
|**18.**|Computação + Kubernetes |Compute/Kubernetes não suporta proxy web NTLM. ||
|**19.**|Compute + web proxy + atualização |Se tiver computado configurado com procuração web, então a atualização do cálculo pode falhar. |Recomendamos que desative o cálculo antes da atualização. |
|**20.**|Kubernetes + atualização |Versões de software anteriores, como lançamentos de 2008, têm um problema de atualização de condições de raça que faz com que a atualização falhe com o ClusterConnectionException. |A utilização das construções mais recentes deve aliviar esta questão. Se ainda vir esta questão, a solução é simplesmente voltar a tentar a atualização, e deve funcionar.|


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Passos seguintes

- [Atualizar o seu dispositivo](azure-stack-edge-gpu-install-update.md)

