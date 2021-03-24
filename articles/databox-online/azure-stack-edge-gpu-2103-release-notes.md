---
title: Notas de lançamento do Azure Stack Edge Pro 2103
description: Descreve questões e resoluções abertas críticas para o lançamento do Azure Stack Edge Pro em execução 2103.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/23/2021
ms.author: alkohli
ms.openlocfilehash: 846d4a259f0fcd204bcad6c898efc999c3765fd3
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962681"
---
# <a name="azure-stack-edge-2103-release-notes"></a>Notas de lançamento do Azure Stack Edge 2103

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para o lançamento de 2103 para os seus dispositivos Azure Stack Edge. Estas notas de lançamento são aplicáveis para os dispositivos Azure Stack Edge Pro GPU, Azure Stack Edge Pro R e Azure Stack Edge Mini R. Funcionalidades e problemas que correspondam a um modelo específico são chamados onde for aplicável.

As notas de lançamento são continuamente atualizadas, e à medida que são descobertas questões críticas que requerem uma solução alternativa, são adicionadas. Antes de implementar o seu dispositivo, reveja cuidadosamente as informações contidas nas notas de lançamento.

Este artigo aplica-se à versão **Azure Stack Edge 2103,** que mapeia para a versão do software número **2.2.1540.2890**. Este software pode ser aplicado ao seu dispositivo se estiver a executar pelo menos o software Azure Stack Edge 2010 (2.1.1377.2170).

## <a name="whats-new"></a>Novidades

As seguintes novas funcionalidades estão disponíveis na versão Azure Stack Edge 2103. 
 
- **Novas funcionalidades para Máquinas Virtuais** - A partir desta versão, pode realizar as seguintes operações de gestão nas máquinas virtuais através do portal Azure:
    - Adicione ou remova várias interfaces de rede a um VM existente.
    - Adicione ou remova vários discos a um VM existente.
    - Redimensionar o VM.
    - Adicione dados personalizados durante a implementação de um Windows ou um Linux VM.

  Também pode [ligar-se à consola VM no seu dispositivo](azure-stack-edge-gpu-connect-virtual-machine-console.md) e resolver problemas com os problemas de VM.

- **Ligue-se à interface PowerShell via https** - A partir desta versão, deixará de ser capaz de abrir uma sessão remota do PowerShell num dispositivo em *http*. Por predefinição, *os https* serão utilizados para todas as sessões. Para obter mais informações, consulte como [ligar-se à interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) do seu dispositivo.

- **Melhorias para o Compute** - Foram feitas várias melhorias e melhorias, incluindo as de:

    - **Qualidade geral da plataforma de computação.** Esta versão tem correções de bugs para melhorar a qualidade global da plataforma de computação. Consulte as [Questões corrigidas no lançamento de 2103](#issues-fixed-in-2103-release). 
    - **Componentes da plataforma computacional.** Foram aplicadas atualizações de segurança à imagem do Compute VM. As versões IoT Edge e Azure Arc para as versões Kubernetes também foram atualizadas.
    - **Diagnósticos**. Uma nova API é lançada para verificar as condições de recursos e de rede. Pode ligar-se à interface PowerShell do dispositivo e utilizar o `Test-HcsKubernetesStatus` comando para verificar a prontidão da rede do dispositivo.
    - **Recolha de registos** que levaria a uma melhor depuração. 
    - **Alertando a infraestrutura** que lhe permitirá detetar conflitos ip para endereços IP para endereços IP compute. 
    - **Misture a carga** de trabalho de Kubernetes e o gestor local de recursos Azure. 

- **Registação proativa por predefinição** - Iniciar esta versão, a recolha de registos proactivos é ativada por padrão no seu dispositivo. Esta funcionalidade permite à Microsoft recolher registos proativamente com base nos indicadores de saúde do sistema para ajudar a resolver eficazmente qualquer problema do dispositivo. Para obter mais informações, consulte [a recolha de registos Proactive no seu dispositivo](azure-stack-edge-gpu-proactive-log-collection.md).

## <a name="issues-fixed-in-2103-release"></a>Emissões corrigidas em lançamento de 2103

A tabela que se segue enumera as questões que foram lançadas em versões anteriores e corrigidas na versão atual.

| N.º | Funcionalidade | Problema | 
| --- | --- | --- |
|**1.**|Kubernetes |O registo do contentor de borda não funciona quando o proxy web está ativado.|
|**2.**|Kubernetes |O registo do contentor de borda não funciona com módulos IoT Edge.| 

## <a name="known-issues-in-2103-release"></a>Questões conhecidas em lançamento de 2103

A tabela seguinte fornece um resumo das questões conhecidas na versão de 2103.

| N.º | Funcionalidade | Problema | Soluções/comentários |
| --- | --- | --- | --- |
|**1.**|Funcionalidades de pré-visualização |Para esta versão, as seguintes funcionalidades: Local Azure Resource Manager, VMs, Gestão em Nuvem de VMs, gestão de nuvem Kubernetes, Azure Arc habilitado a Kubernetes, VPN para Azure Stack Edge Pro R e Azure Stack Edge Mini R, serviço multi-processo (MPS) para GPU Azure Stack Edge Pro - estão todos disponíveis em pré-visualização.  |Estas funcionalidades estarão geralmente disponíveis em lançamentos posteriores. |
|**2.**|GPU VMs |Antes desta versão, o ciclo de vida da GPU VM não foi gerido no fluxo de atualização. Assim, ao atualizar para o lançamento de 2103, os VMs da GPU não são interrompidos automaticamente durante a atualização. Terá de parar manualmente os VMs da GPU utilizando uma `stop-stayProvisioned` bandeira antes de atualizar o seu dispositivo. Para obter mais informações, consulte [Suspender ou desligar o VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm).<br> Todos os VMs da GPU que são mantidos em funcionamento antes da atualização, são iniciados após a atualização. Nestes casos, as cargas de trabalho que correm nos VMs não são terminadas graciosamente. E os VMs podem potencialmente acabar num estado indesejável após a atualização. <br>Todos os VMs da GPU que são parados através da `stop-stayProvisioned` atualização, são automaticamente iniciados após a atualização. <br>Se parar os VMs gpu através do portal Azure, terá de iniciar manualmente o VM após a atualização do dispositivo.| Se correr VMs gpu com Kubernetes, pare os VMs GPU antes da atualização. <br>Quando os VMs da GPU forem parados, kubernetes assumirá as GPUs que foram originalmente usadas por VMs. <br>Quanto mais tempo os VMs da GPU estiverem em estado parado, maiores são as hipóteses de Kubernetes assumir as GPUs. |
|**3.**|Extensão VM de script personalizado |Existe um problema conhecido nos VMs do Windows que foram criados numa versão anterior e o dispositivo foi atualizado para 2103. <br> Se adicionar uma extensão de script personalizada nestes VMs, o Windows VM Guest Agent (apenas versão 2.7.41491.901) fica preso na atualização, fazendo com que a extensão seja despasada. | Para contornar este problema: <ul><li> Ligue-se ao Windows VM utilizando o protocolo de ambiente de trabalho remoto (RDP). </li><li> Certifique-se de que o `waappagent.exe` funcionamento da máquina: `Get-Process WaAppAgent` . </li><li> Se `waappagent.exe` o não estiver a funcionar, reinicie o `rdagent` serviço: `Get-Service RdAgent` \| `Restart-Service` . Espere 5 minutos.</li><li> Enquanto o `waappagent.exe` processo está a decorrer, mata o `WindowsAzureGuest.exe` processo. </li><li>Depois de eliminar o processo, o processo recomeça a funcionar com a versão mais recente.</li><li>Verifique se a versão do Windows VM Guest Agent é 2.7.41491.971 utilizando este comando: `Get-Process WindowsAzureGuestAgent` \| `fl ProductVersion` .</li><li>[Configurar a extensão de script personalizada no Windows VM](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md). </li><ul> | 
|**4.**|Serviço multi-processo (MPS) |Quando o software do dispositivo e o cluster Kubernetes são atualizados, a definição de MPS não é retida para as cargas de trabalho.   |[Re-capacitar o MPS](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) e recolocar as cargas de trabalho que utilizavam MPS. |


## <a name="known-issues-from-previous-releases"></a>Questões conhecidas de lançamentos anteriores

O quadro seguinte fornece um resumo das questões conhecidas transitadas das versões anteriores.

| N.º | Funcionalidade | Problema | Soluções/comentários |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | A criação da base de dados SQL requer acesso ao Administrador.   |Faça os seguintes passos em vez dos passos 1-2 em [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) . <ul><li>Na UI local do seu dispositivo, ative a interface compute. **Selecione Compute > Port # > Ative para calcular > Apply.**</li><li>Faça o download `sqlcmd` na sua máquina de clientes a partir de https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Ligue-se ao endereço IP da interface de computação (a porta que estava ativada), adicionando um "1401" ao final do endereço.</li><li>O comando final será assim: sqlcmd -S {Interface IP},1401 -U SA -P "Forte! Passw0rd".</li>Depois disso, os passos 3-4 da documentação atual devem ser idênticos. </li></ul> |
| **2.** |Atualizar| Alterações incrementais nas bolhas restauradas via **Refresh** NÃO são suportadas |No caso dos pontos finais blob, as atualizações parciais de blobs após uma Atualização, podem resultar em que as atualizações não sejam carregadas na nuvem. Por exemplo, sequência de ações como:<ul><li>Crie bolha na nuvem. Ou elimine uma bolha previamente carregada do dispositivo.</li><li>Refresque a bolha da nuvem para o aparelho utilizando a funcionalidade de atualização.</li><li>Atualize apenas uma parte da bolha utilizando APIs de REST Azure SDK.</li></ul>Estas ações podem resultar em secções atualizadas da bolha para não ser atualizada na nuvem. <br>**Solução alternativa**: Utilize ferramentas como robocopia, ou cópia de ficheiro regular através do Explorer ou da linha de comando, para substituir bolhas inteiras.|
|**3.**|Limitação|Durante o estrangulamento, se não forem permitidas novas gravações para o dispositivo, escreve o cliente NFS falhando com um erro de "Permissão Negada".| O erro mostrará o seguinte:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: não é possível criar 'teste' de diretório: Permissão negada|
|**4.**|Ingestão de armazenamento blob|Ao utilizar a versão 10 do AzCopy para a ingestão de armazenamento de Blob, execute a AzCopy com o seguinte argumento: `Azcopy <other arguments> --cap-mbps 2000`| Se estes limites não forem fornecidos para a AzCopy, pode potencialmente enviar um grande número de pedidos para o dispositivo, resultando em problemas com o serviço.|
|**5.**|Contas de armazenamento hierárquico|Aplicam-se as seguintes contas de armazenamento hierárquicos:<ul><li> Apenas bolhas de blocos são suportadas. As bolhas de página não são suportadas.</li><li>Não existe instantâneo ou suporte de cópia da API.</li><li> A ingestão de carga de trabalho de Hadoop `distcp` através não é suportada, uma vez que utiliza fortemente a operação de cópia.</li></ul>||
|**6.**|Ligação de partilha NFS|Se vários processos estiverem a copiar para a mesma parte, e o `nolock` atributo não for utilizado, poderá ver erros durante a cópia.|O `nolock` atributo deve ser passado para o comando de montagem para copiar ficheiros para a partilha NFS. Por exemplo: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Aglomerado de Kubernetes|Ao aplicar uma atualização no seu dispositivo que esteja a executar um cluster Kubernetes, as máquinas virtuais Kubernetes reiniciarão e reiniciarão. Neste caso, apenas as cápsulas que são implantadas com réplicas especificadas são automaticamente restauradas após uma atualização.  |Se tiver criado cápsulas individuais fora de um controlador de replicação sem especificar um conjunto de réplicas, estas cápsulas não serão restauradas automaticamente após a atualização do dispositivo. Vai precisar restaurar estas cápsulas.<br>Um conjunto de réplicas substitui as cápsulas que são eliminadas ou terminadas por qualquer motivo, tais como falha de nó ou atualização de nó disruptivo. Por esta razão, recomendamos que utilize um conjunto de réplicas mesmo que a sua aplicação exija apenas uma única cápsula.|
|**8.**|Aglomerado de Kubernetes|Kubernetes em Azure Stack Edge Pro é suportado apenas com Helm v3 ou mais tarde. Para mais informações, aceda a [perguntas frequentes: Remoção de Tiller](https://v3.helm.sh/docs/faq/).|
|**9.**|Kubernetes ativado pelo Azure Arc |Para a versão GA, a Azure Arc habilitada a Kubernetes é atualizada da versão 0.1.18 para 0.2.9. Uma vez que a atualização de Kubernetes ativada pelo Arco Azure não é suportada no dispositivo Azure Stack Edge, terá de recolocar o Azure Arc ativado por Kubernetes.|Siga estes passos:<ol><li>[Aplicar software de dispositivos e atualizações de Kubernetes](azure-stack-edge-gpu-install-update.md).</li><li>Ligue-se à [interface PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md).</li><li>Retire o agente Azure Arc existente. Tipo: `Remove-HcsKubernetesAzureArcAgent` .</li><li>Implementar [o Arco azul para um novo recurso](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md). Não utilize um recurso Azure Arc existente.</li></ol>|
|**10.**|Kubernetes ativado pelo Azure Arc|As implementações do Azure Arc não são suportadas se o representante web estiver configurado no seu dispositivo Azure Stack Edge Pro.||
|**11.**|Kubernetes |A porta 31000 está reservada para o Painel Kubernetes. A porta 31001 está reservada para o registo do contentor Edge. Da mesma forma, na configuração padrão, os endereços IP 172.28.0.1 e 172.28.0.10, são reservados para o serviço Kubernetes e o serviço Core DNS, respectivamente.|Não utilize os IPs reservados.|
|**12.**|Kubernetes |A Kubernetes não permite atualmente serviços loadBalancer com vários protocolos. Por exemplo, um serviço DNS que teria de ouvir tanto o TCP como a UDP. |Para contornar esta limitação de Kubernetes com MetalLB, dois serviços (um para TCP, um para UDP) podem ser criados no mesmo seletor de vagem. Estes serviços utilizam a mesma chave de partilha e spec.loadBalancerIP para partilhar o mesmo endereço IP. Os IPs também podem ser partilhados se tiver mais serviços do que endereços IP disponíveis. <br> Para mais informações, consulte [a partilha de endereços IP.](https://metallb.universe.tf/usage/#ip-address-sharing)|
|**13.**|Aglomerado de Kubernetes|Os módulos de mercado Azure IoT Edge existentes podem necessitar de modificações para funcionar no dispositivo IoT Edge no dispositivo Azure Stack Edge.|Para obter mais informações, consulte modificar os módulos Azure IoT Edge do mercado para executar no dispositivo Azure Stack Edge.<!-- insert link-->|
|**14.**|Kubernetes |Os suportes de encaixe baseados em ficheiros não são suportados com Azure IoT Edge em Kubernetes no dispositivo Azure Stack Edge.|IoT Edge usa uma camada de tradução para traduzir `ContainerCreate` opções para construções de Kubernetes. A criação `Binds` de mapas para `hostpath` o diretório e, portanto, os suportes de ligação baseados em ficheiros não podem ser ligados a caminhos em contentores IoT Edge. Se possível, mapear o diretório dos pais.|
|**15.**|Kubernetes |Se trouxer os seus próprios certificados para IoT Edge e adicionar esses certificados no seu dispositivo Azure Stack Edge após a configuração do cálculo no dispositivo, os novos certificados não são recolhidos.|Para resolver este problema, deverá fazer o upload dos certificados antes de configurar o cálculo no dispositivo. Se o cálculo já estiver configurado, [ligue-se à interface PowerShell do dispositivo e executar comandos IoT Edge](azure-stack-edge-gpu-connect-powershell-interface.md#use-iotedge-commands). Reiniciar `iotedged` e `edgehub` pods.|
|**16.**|Certificados |Em certos casos, o estado de certificação na UI local pode demorar vários segundos a ser atualizado. |Os seguintes cenários na UI local podem ser afetados.<ul><li>**Coluna de estado** na página **de Certificados.**</li><li>**Azulejo** de segurança na página **Get start.**</li><li>Azulejo de **configuração** na página **overview.**</li></ul>  |
|**17.**|IoT Edge |Os módulos implantados através do IoT Edge não podem utilizar a rede de anfitriões. | |
|**18.**|Computação + Kubernetes |Compute/Kubernetes não suporta proxy web NTLM. ||
|**19.**|Kubernetes + atualização |Versões de software anteriores, como lançamentos de 2008, têm um problema de atualização de condições de raça que faz com que a atualização falhe com o ClusterConnectionException. |A utilização das construções mais recentes deve ajudar a evitar este problema. Se ainda vir esta questão, a solução é voltar a tentar a atualização, e deve funcionar.|
|**20**|Internet Explorer|Se as funcionalidades de segurança reforçadas estiverem ativadas, poderá não conseguir aceder às páginas locais da Web UI. | Desative a segurança reforçada e reinicie o seu navegador.|
|**21.**|Painel de Kubernetes | O ponto final *https* para o Painel Kubernetes com certificado SSL não é suportado. | |
|**22.**|Kubernetes |Kubernetes não suporta " em nomes variáveis ambientais que são usados por aplicações .NET. Isto também é necessário para que o módulo IoT Edge da grelha de evento funcione no dispositivo Azure Stack Edge e outras aplicações. Para mais informações, consulte [ASP.NET documentação principal.](/aspnet/core/fundamentals/configuration/?tabs=basicconfiguration&view=aspnetcore-3.1&preserve-view=true#environment-variables)|Substitua " por duplo sublinhado. Para mais informações,consulte [a edição de Kubernetes](https://github.com/kubernetes/kubernetes/issues/53201)|
|**23.** |Aglomerado Azure Arc + Kubernetes |Por predefinição, quando os recursos `yamls` são eliminados do repositório git, os recursos correspondentes não são eliminados do cluster Kubernetes.  |Para permitir a supressão de recursos quando são eliminados do repositório de git, definido `--sync-garbage-collection` no Arc OperatorParams. Para obter mais informações, consulte [Eliminar uma configuração](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters). |
|**24.**|NFS |As aplicações que utilizam suportes de partilha NFS no seu dispositivo para escrever dados devem utilizar a escrita exclusiva. Isto garante que as escritas são escritas no disco.| |
|**25.**|Configuração do cálculo |A configuração do cálculo falha nas configurações de rede onde gateways ou switches ou routers respondem aos pedidos do Address Resolution Protocol (ARP) para sistemas que não existem na rede.| |
|**26.**|Computação e Kubernetes |Se a Kubernetes for configurada primeiro no seu dispositivo, reclama todas as GPUs disponíveis. Assim, não é possível criar VMs Azure Resource Manager usando GPUs após a configuração dos Kubernetes. |Se o seu dispositivo tiver 2 GPUs, então pode criar 1 VM que usa a GPU e, em seguida, configurar Kubernetes. Neste caso, a Kubernetes utilizará os restantes 1 GPU disponíveis. |


<!--|**18.**|Azure Private Edge Zone (Preview) |There is a known issue with Virtual Network Function VM if the VM was created on Azure Stack Edge device running earlier preview builds such as 2006/2007b and then the device was updated to 2009 GA release. The issue is that the VNF information can't be retrieved or any new VNFs can't be created unless the VNF VMs are deleted before the device is updated.  |Before you update Azure Stack Edge device to 2009 release, use the PowerShell command `get-mecvnf` followed by `remove-mecvnf <VNF guid>` to remove all Virtual Network Function VMs one at a time. After the upgrade, you will need to redeploy the same VNFs.|-->


## <a name="next-steps"></a>Passos seguintes

- [Atualizar o seu dispositivo](azure-stack-edge-gpu-install-update.md)
