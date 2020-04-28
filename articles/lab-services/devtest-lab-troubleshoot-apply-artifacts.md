---
title: Problemas de resolução de problemas com artefactos em Azure DevTest Labs Microsoft Docs
description: Aprenda a resolver problemas que ocorrem ao aplicar artefactos numa máquina virtual Azure DevTest Labs.
services: devtest-lab
documentationcenter: na
author: spelluru
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: fc5051667100a2ebaa01b7815f825fadd766b08f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75456983"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Problemas de resolução de problemas ao aplicar artefactos numa máquina virtual azure DevTest Labs
Aplicar artefactos numa máquina virtual pode falhar por várias razões. Este artigo guia-o através de alguns dos métodos para ajudar a identificar possíveis causas.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure DevTest Labs (DTL) nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter Suporte.   

> [!NOTE]
> Este artigo aplica-se tanto a máquinas virtuais Windows como não Windows. Embora existam algumas diferenças, serão explicitamente chamadas neste artigo.

## <a name="quick-troubleshooting-steps"></a>Passos rápidos de resolução de problemas
Verifique se o VM está a funcionar. A DevTest Labs exige que o VM esteja em funcionamento e que o Agente Virtual da [Máquina Do Microsoft Azure (VM Agent)](../virtual-machines/extensions/agent-windows.md) esteja instalado e pronto.

> [!TIP]
> No **portal Azure,** navegue até à página **de artefactos de Gestão** para o VM para ver se o VM está pronto para aplicar artefactos. Vê-se uma mensagem no topo da página. 
> 
> Utilizando **o Azure PowerShell,** inspecione os **canAtos**de bandeira, que só são devolvidos quando expandir numa operação GET. Consulte o seguinte comando exemplo:

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

## <a name="ways-to-troubleshoot"></a>Formas de resolver problemas 
Pode resolver os Problemas com vMs criados utilizando o DevTest Labs e o modelo de implementação do Gestor de Recursos utilizando um dos seguintes métodos:

- **Portal Azure** - ótimo se precisar rapidamente de obter uma pista visual do que pode estar a causar o problema.
- **Azure PowerShell** - se estiver confortável com um pedido powerShell, consulta rapidamente os recursos da DevTest Labs utilizando os cmdlets Azure PowerShell.

> [!TIP]
> Para obter mais informações sobre como rever a execução de artefactos dentro de um VM, consulte [falhas de artefactos diagnosticar em laboratório](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="symptoms-causes-and-potential-resolutions"></a>Sintomas, causas e potenciais resoluções 

### <a name="artifact-appears-to-hang"></a>Artefacto parece pendurar   
Um artefacto parece ser pendurado até expirar um período de tempo pré-definido, e o artefacto é marcado como **Falhado**.

Quando um artefacto parece ser enforcado, primeiro determina onde está preso. Um artefacto pode ser bloqueado em qualquer um dos seguintes passos durante a execução:

- **Durante o pedido inicial.** A DevTest Labs cria um modelo de Gestor de Recursos Azure para solicitar a utilização da extensão personalizada do script (CSE). Portanto, nos bastidores, é desencadeada uma implantação de um grupo de recursos. Quando um erro a este nível acontece, obtém-se detalhes nos **Registos** de Atividade do grupo de recursos para o VM em questão.  
    - Pode aceder ao registo de atividade da barra de navegação da página VM do laboratório. Ao selecioná-lo, vê uma entrada para **aplicar artefactos a máquina virtual** (se a operação de artefactos de aplicação foi ativada diretamente) ou adicionar ou modificar **máquinas virtuais** (se a operação de artefactos aplicados fizesse parte do processo de criação vm).
    - Procure erros nestas entradas. Às vezes, o erro não será marcado em conformidade, e terá que investigar cada entrada.
    - Ao investigar os detalhes de cada entrada, certifique-se de rever o conteúdo da carga útil jSON. Pode ver um erro na parte inferior do documento.
- **Ao tentar executar o artefacto.** Pode ser devido a problemas de networking ou armazenamento. Consulte a secção respetiva mais tarde neste artigo para mais detalhes. Também pode acontecer devido à forma como o guião é da autoria. Por exemplo:
    - Um script PowerShell tem **parâmetros obrigatórios,** mas não se passa um valor, quer porque permite que o utilizador o deixe em branco, quer porque não tem um valor predefinido para a propriedade no ficheiro de definição artifactfile.json. O script será pendurado porque aguarda a entrada do utilizador.
    - Um script PowerShell **requer entrada** do utilizador como parte da execução. Os scripts devem ser escritos para funcionar em silêncio sem necessitar de qualquer intervenção do utilizador.
- **O Agente VM demora muito a estar pronto.** Quando o VM é iniciado pela primeira vez, ou quando a extensão do script personalizado é instalada pela primeira vez para servir o pedido de aplicação de artefactos, o VM pode exigir a atualização do Agente VM ou esperar que o Agente VM inicialize. Pode haver serviços de que o Agente VM depende que estão a demorar muito tempo a inicializar. Nestes casos, consulte a visão geral do [Agente De Máquinas Virtuais azure](../virtual-machines/extensions/agent-windows.md) para mais resolução de problemas.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>Para verificar se o artefacto parece estar pendurado por causa do script

1. Inicie sessão na máquina virtual em questão.
2. Copie o script localmente na máquina virtual `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`ou localize-o na máquina virtual em baixo . É o local onde os scripts de artefactos são descarregados.
3. Utilizando um pedido de comando elevado, execute o script localmente, fornecendo os mesmos valores de parâmetro utilizados para causar o problema.
4. Determine se o guião sofre de algum comportamento indesejado. Em caso afirmativo, solicite uma atualização do artefacto (se for do representante público); ou, faça as correções por si mesmo (se for do seu repo privado).

> [!TIP]
> Pode corrigir problemas com artefactos hospedados no nosso [repo público](https://github.com/Azure/azure-devtestlab) e submeter as alterações para a nossa revisão e aprovação. Consulte a secção **Contribuições** no documento [README.md.](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md)
> 
> Para obter informações sobre a escrita dos seus próprios artefactos, consulte [AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) documento.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>Para verificar se o artefacto parece estar pendurado por causa do Agente VM:
1. Inicie sessão na máquina virtual em questão.
2. Utilizando o File Explorer navegar para **c:\WindowsAzure\logs**.
3. Localizar e abrir **ficheiro WaAppAgent.log**.
4. Procure entradas que mostrem quando o Agente VM começa e quando está a terminar a inicialização (isto é, o primeiro batimento cardíaco é enviado). Favoreça entradas mais recentes ou especificamente as do período de tempo para o qual experimenta o problema.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    Neste exemplo, pode ver que o tempo de início do Agente VM demorou 10 minutos e 20 segundos porque foi enviado um batimento cardíaco. A causa neste caso foi que o serviço OOBE demorou muito tempo a começar.

> [!TIP]
> Para obter informações gerais sobre extensões Azure, consulte [extensões e funcionalidades da máquina virtual Azure.](../virtual-machines/extensions/overview.md)

## <a name="storage-errors"></a>Erros de armazenamento
A DevTest Labs requer acesso à conta de armazenamento do laboratório que é criada para cache artefactos. Quando a DevTest Labs aplicar um artefacto, irá ler a configuração do artefacto e os seus ficheiros a partir dos repositórios configurados. Por padrão, a DevTest Labs configura o acesso ao repo de **artefactos públicos.**

Dependendo da configuração de um VM, pode não ter acesso direto a este repo. Portanto, por design, o DevTest Labs coloca os artefactos numa conta de armazenamento que é criada quando o laboratório é inicializado pela primeira vez.

Se o acesso a esta conta de armazenamento for bloqueado de alguma forma, como quando o tráfego está bloqueado do VM para o serviço de Armazenamento Azure, poderá ver um erro semelhante ao seguinte:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

O erro acima apareceria na secção mensagem de **implantação** na página **de resultados** do Artefacto sob **artefactos de gestão**. Também aparecerá nos **Registos** de Atividade sob o grupo de recursos da máquina virtual em questão.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Para garantir que a comunicação ao serviço de armazenamento Azure não está a ser bloqueada:

- **Verifique se há grupos de segurança de rede adicionados (NSG)**. Pode ser que tenha sido adicionada uma política de subscrição onde os NSGs são automaticamente configurados em todas as redes virtuais. Também afetaria a rede virtual padrão do laboratório, se utilizada, ou outra rede virtual configurada no seu laboratório, usada para a criação de VMs.
- **Verifique a conta** de armazenamento do laboratório padrão (isto é, a primeira conta de armazenamento criada quando o laboratório foi criado, cujo\<nome\>geralmente começa com a letra "a" e termina com um número de vários dígitos que é, um nome de laboratório #).
    1. Navegue para o grupo de recursos do laboratório.
    2. Localize o recurso da conta de **armazenamento**de tipo, cujo nome corresponde à convenção.
    3. Navegue para a página da conta de armazenamento chamada **Firewalls e redes virtuais**.
    4. Certifique-se de que está definido para **todas as redes.** Se a opção de **redes Selecionadas** for selecionada, certifique-se de que as redes virtuais do laboratório utilizadas para criar VMs são adicionadas à lista.

Para uma resolução mais aprofundada de problemas, consulte as firewalls de [Armazenamento Configure Azure e redes virtuais](../storage/common/storage-network-security.md).

> [!TIP]
> **Verifique as regras do grupo**de segurança da rede . Utilize a [verificação](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) do fluxo IP para confirmar que uma regra de um grupo de segurança de rede está bloqueando o tráfego de ou para uma máquina virtual. Também pode rever regras eficazes do grupo de segurança para garantir a existência da regra de **indistente** do NSG. Para mais informações, consulte A utilização de regras de [segurança eficazes para resolver o fluxo](../virtual-network/diagnose-network-traffic-filter-problem.md)de tráfego vm .

## <a name="other-sources-of-error"></a>Outras fontes de erro
Existem outras fontes de erro menos frequentes possíveis. Certifique-se de avaliar cada um para ver se se aplica ao seu caso. Aqui está um deles: 

- Ficha de **acesso pessoal expirado para o repo privado**. Quando expirado, o artefacto não será listado e quaisquer scripts que se refiram a artefactos de um repositório com um token de acesso privado expirado falharão em conformidade.

## <a name="next-steps"></a>Passos seguintes
Se nenhum destes erros ocorrer e ainda não conseguir aplicar artefactos, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

