---
title: Problemas de resolução de problemas com artefactos em Azure DevTest Labs | Microsoft Docs
description: Saiba como resolver problemas que ocorrem ao aplicar artefactos numa máquina virtual Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a89b675a1b3bf134b98e09c7278f0eccb594c325
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483198"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Problemas de resolução de problemas ao aplicar artefactos numa máquina virtual Azure DevTest Labs
A aplicação de artefactos numa máquina virtual pode falhar por várias razões. Este artigo guia-o através de alguns dos métodos para ajudar a identificar possíveis causas.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure DevTest Labs (DTL) nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter Suporte.   

> [!NOTE]
> Este artigo aplica-se tanto a máquinas virtuais Windows como não-Windows. Embora existam algumas diferenças, serão explicitamente chamados neste artigo.

## <a name="quick-troubleshooting-steps"></a>Passos rápidos de resolução de problemas
Verifique se o VM está a funcionar. A DevTest Labs exige que o VM esteja em funcionamento e que o [Agente de Máquinas Virtuais (Agente VM) da Microsoft Azure](../virtual-machines/extensions/agent-windows.md) esteja instalado e pronto.

> [!TIP]
> No **portal Azure,** navegue na página **de artefactos Manage** para o VM para ver se o VM está pronto para aplicar artefactos. Vês uma mensagem no topo da página. 
> 
> Utilizando **a Azure PowerShell,** inspecione a lata **de bandeiraSApplyArtifacts**, que só é devolvida quando expandir numa operação GET. Consulte o seguinte comando de exemplo:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Formas de resolução de problemas 
Pode resolver problemas de VMs criados utilizando a DevTest Labs e o modelo de implementação do Gestor de Recursos utilizando um dos seguintes métodos:

- **Portal Azure** - ótimo se você precisa obter rapidamente uma pista visual do que pode estar causando o problema.
- **Azure PowerShell** - se estiver confortável com uma solicitação PowerShell, questione rapidamente os recursos da DevTest Labs utilizando os cmdlets Azure PowerShell.

> [!TIP]
> Para obter mais informações sobre como rever a execução de artefactos dentro de um VM, consulte [falhas de artefactos de diagnóstico no laboratório.](devtest-lab-troubleshoot-artifact-failure.md)

## <a name="symptoms-causes-and-potential-resolutions"></a>Sintomas, causas e potenciais resoluções 

### <a name="artifact-appears-to-stop-responding"></a>O artefacto parece parar de responder

Um artefacto parece parar de responder até que um período de tempo pré-definido expire, e o artefacto é marcado como **Falhado**.

Quando um artefacto parece estar pendurado, primeiro determinar onde está preso. Um artefacto pode ser bloqueado em qualquer um dos seguintes passos durante a execução:

- **Durante o pedido inicial.** DevTest Labs cria um modelo de Gestor de Recursos Azure para solicitar a utilização da Extensão de Script Personalizado (CSE). Portanto, nos bastidores, uma implantação de grupo de recursos é desencadeada. Quando um erro a este nível acontece, obtém-se detalhes nos **Registos** de Atividade do grupo de recursos para o VM em questão.  
    - Pode aceder ao registo de atividades da barra de navegação de página de VM do laboratório. Ao selecioná-lo, vê-se uma entrada para **aplicar artefactos à máquina virtual** (se a operação de artefactos aplicados foi acionada diretamente) ou adicionar ou **modificar máquinas virtuais** (se a operação de artefactos de aplicação fizesse parte do processo de criação de VM).
    - Procure erros nestas entradas. Às vezes, o erro não será marcado em conformidade, e terá que investigar cada entrada.
    - Ao investigar os detalhes de cada entrada, certifique-se de rever o conteúdo da carga útil JSON. Pode ver um erro na parte inferior do documento.
- **Ao tentar executar o artefacto.** Pode ser devido a problemas de networking ou armazenamento. Consulte a secção respetivo mais tarde neste artigo para mais detalhes. Também pode acontecer devido à forma como o guião é da autoria. Por exemplo:
    - Um script PowerShell tem **parâmetros obrigatórios**, mas não lhe passa um valor, quer porque permite que o utilizador o deixe em branco, quer porque não tem um valor predefinido para a propriedade no artifactfile.jsno ficheiro de definição. O script deixará de responder porque aguarda a entrada do utilizador.
    - Um script PowerShell **requer a entrada** do utilizador como parte da execução. Os scripts devem ser escritos para funcionar em silêncio sem exigir qualquer intervenção do utilizador.
- **O Agente VM demora muito a estar pronto.** Quando o VM é iniciado pela primeira vez, ou quando a extensão de script personalizada é instalada pela primeira vez para servir o pedido de aplicação de artefactos, o VM pode exigir a atualização do Agente VM ou esperar que o Agente VM inicialize. Pode haver serviços de que depende o Agente VM que estão a demorar muito tempo a ser inicializ. Nestes casos, consulte [a visão geral do Agente de Máquinas Virtuais Azure](../virtual-machines/extensions/agent-windows.md) para uma maior resolução de problemas.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>Para verificar se o artefacto parece parar de responder por causa do script

1. Faça login na máquina virtual em questão.
2. Copie o script localmente na máquina virtual ou localize-o na máquina virtual sob `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>` . É o local onde os scripts dos artefactos são descarregados.
3. Utilizando um pedido de comando elevado, execute o script localmente, fornecendo os mesmos valores de parâmetro utilizados para causar o problema.
4. Determine se o script sofre de algum comportamento indesejado. Em caso afirmativo, ou solicita uma atualização do artefacto (se for do repo público); ou, faça as correções por si mesmo (se for do seu repo privado).

> [!TIP]
> Pode corrigir problemas com artefactos alojados no nosso [repo público](https://github.com/Azure/azure-devtestlab) e submeter as alterações para a nossa revisão e aprovação. Consulte a secção **Contribuições** no documento [README.md.](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md)
> 
> Para obter informações sobre a escrita dos seus próprios artefactos, consulte [AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) documento.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>Para verificar se o artefacto parece parar de responder por causa do agente VM:
1. Faça login na máquina virtual em questão.
2. Utilizando o Explorador de Ficheiros, navegue para **C:\WindowsAzure\logs**.
3. Localizar e abrir o ficheiro **WaAppAgent.log**.
4. Procure entradas que mostrem quando o Agente VM começa e quando está terminando a inicialização (isto é, o primeiro batimento cardíaco é enviado). Favorecer entradas mais recentes ou especificamente as que se rodeiam o período de tempo para o qual você experimenta o problema.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    Neste exemplo, pode ver-se que o tempo de início do Agente VM demorou 10 minutos e 20 segundos porque foi enviado um batimento cardíaco. A causa neste caso foi o serviço OOBE que demorou muito tempo a começar.

> [!TIP]
> Para obter informações gerais sobre extensões Azure, consulte [extensões e funcionalidades de máquinas virtuais Azure](../virtual-machines/extensions/overview.md).

## <a name="storage-errors"></a>Erros de armazenamento
A DevTest Labs requer acesso à conta de armazenamento do laboratório que é criada para cache de artefactos. Quando a DevTest Labs aplica um artefacto, irá ler a configuração do artefacto e os seus ficheiros dos repositórios configurados. Por defeito, a DevTest Labs configura o acesso ao **repo de artefactos públicos.**

Dependendo da configuração de um VM, pode não ter acesso direto a este repo. Portanto, por design, a DevTest Labs caches os artefactos numa conta de armazenamento que é criada quando o laboratório é inicializado pela primeira vez.

Se o acesso a esta conta de armazenamento for bloqueado de alguma forma, como quando o tráfego é bloqueado do VM para o serviço de Armazenamento Azure, poderá ver um erro semelhante ao seguinte:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

O erro acima apareceria na secção **de mensagem de implantação** na página de **resultados** do Artefacto sob **o Comando de Artefactos**. Também aparecerá nos **Registos de Atividade** no âmbito do grupo de recursos da máquina virtual em questão.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Para garantir que a comunicação ao serviço de armazenamento Azure não está a ser bloqueada:

- **Verifique se há grupos de segurança de rede adicionados (NSG)**. Pode ser que tenha sido adicionada uma política de subscrição em que os NSGs são automaticamente configurados em todas as redes virtuais. Também afetaria a rede virtual padrão do laboratório, se usada, ou outra rede virtual configurada no seu laboratório, usada para a criação de VMs.
- **Verifique a conta de armazenamento do laboratório predefinido** (isto é, a primeira conta de armazenamento criada quando o laboratório foi criado, cujo nome geralmente começa com a letra "a" e termina com um número de vários dígitos que é, um \<labname\> #).
    1. Navegue para o grupo de recursos do laboratório.
    2. Localize o recurso da conta de **armazenamento** do tipo, cujo nome corresponde à convenção.
    3. Navegue para a página de conta de armazenamento chamada **Firewalls e redes virtuais**.
    4. Certifique-se de que está definido para **todas as redes**. Se a opção **de redes selecionada** for selecionada, certifique-se de que as redes virtuais do laboratório utilizadas para criar VMs são adicionadas à lista.

Para uma resolução mais aprofundada de problemas, consulte [as firewalls de armazenamento Configure Azure e as redes virtuais.](../storage/common/storage-network-security.md)

> [!TIP]
> **Verifique as regras do grupo de segurança de rede**. Utilize [o fluxo IP para](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) confirmar que uma regra de um grupo de segurança de rede está a bloquear o tráfego de ou para uma máquina virtual. Também pode rever regras eficazes do grupo de segurança para garantir que **a** regra NSG de entrada existe. Para obter mais informações, consulte [a utilização de regras de segurança eficazes para resolver o fluxo de tráfego de VM.](../virtual-network/diagnose-network-traffic-filter-problem.md)

## <a name="other-sources-of-error"></a>Outras fontes de erro
Existem outras fontes de erro menos frequentes. Certifique-se de avaliar cada um para ver se se aplica ao seu caso. Aqui está um deles: 

- **Sinal de acesso pessoal expirado para o repo privado**. Quando expirado, o artefacto não será listado e quaisquer scripts que se refiram a artefactos de um repositório com um token de acesso privado expirado falharão em conformidade.

## <a name="next-steps"></a>Passos seguintes
Se nenhum destes erros ocorrer e ainda não conseguir aplicar artefactos, pode apresentar um incidente de suporte ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.
