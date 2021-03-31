---
title: Executar o planejador de implementação para recuperação de desastres VMware com recuperação do local de Azure
description: Este artigo descreve como executar o Planejador de Implementação de Recuperação do Site Azure para a recuperação de desastres VMware para Azure.
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: ramamill
ms.openlocfilehash: 05d260de726c62c130a58938c2a2c9fa2440a96d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004726"
---
# <a name="run-the-deployment-planner-for-vmware-disaster-recovery"></a>Executar o Planejador de Implementação para recuperação de desastres VMware
Este artigo é o manual do utilizador do Azure Site Recovery Deployment Planner para implementações de produção de VMware para o Azure.


## <a name="modes-of-running-deployment-planner"></a>Modos de executar o planeador de implementação
Pode executar a ferramenta de linha de comandos (ASRDeploymentPlanner.exe) num dos três modos seguintes:

1.  [Perfis](#profile-vmware-vms)
2.  [Geração de relatórios](#generate-report)
3.  [Obtenção de débito](#get-throughput)

Em primeiro lugar, execute a ferramenta no modo de criação de perfis, para recolher IOPS e alterações a dados da VM. Em seguida, execute a ferramenta para gerar o relatório para localizar os requisitos de largura de banda de rede e de armazenamento e o custo de DR.

## <a name="profile-vmware-vms"></a>Perfil de VMs VMware
No modo de criação de perfis, a ferramenta Deployment Planner liga-se ao vCenter Server/anfitrião ESXi do vSphere para recolher dados de desempenho da VM.

* A criação de perfis não afeta o desempenho das VMs de produção, porque não é feita nenhuma ligação direta para as mesmas. Todos os dados de desempenho são recolhidos a partir do vCenter Server/anfitrião ESXi do vSphere.
* Para garantir que a criação de perfis tem um impacto mínimo no servidor, a ferramenta consulta o vCenter Server/anfitrião ESXi do vSphere a cada quinze minutos. Este intervalo de consulta não põe em causa a precisão da criação de perfis, porque a ferramenta armazena os dados dos contadores de desempenho relativos a cada minuto.

### <a name="create-a-list-of-vms-to-profile"></a>Criar uma lista de VMs para as quais criar perfis
Em primeiro lugar, precisa de uma lista das VMs para as quais vão ser criados perfis. Pode utilizar os comandos do VMware vSphere PowerCLI no procedimento seguinte para obter todos os nomes de VMs num vCenter Server/anfitrião ESXi do vSphere. Em alternativa, pode listar, num ficheiro, os nomes amigáveis ou os endereços IP das VMs para as quais quer criar perfis manualmente.

1. Inicie sessão na VM na qual o VMware vSphere PowerCLI está instalado.
2. Abra a consola do VMware vSphere PowerCLI.
3. Confirme que a política de execução está ativada no script. Se estiver desativada, abra a consola do VMware vSphere PowerCLI no modo de administrador e ative-a, executando o comando seguinte:

    ```powershell
    Set-ExecutionPolicy –ExecutionPolicy AllSigned
    ```

4. Pode, opcionalmente, ter de executar o seguinte comando se Connect-VIServer não for reconhecido como o nome de cmdlet.

    ```powershell
    Add-PSSnapin VMware.VimAutomation.Core
    ```

5. Para obter todos os nomes de VMs num vCenter Server/anfitrião ESXi do vSphere e armazená-los num ficheiro .txt, execute os dois comandos listados aqui.
Substitua &lsaquo;server name&rsaquo;, &lsaquo;user name&rsaquo;, &lsaquo;password&rsaquo;, &lsaquo;outputfile.txt&rsaquo; pelas suas entradas.

    ```powershell
    Connect-VIServer -Server <server name> -User <user name> -Password <password>

    Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>
    ```

6. Abra o ficheiro de saída no Bloco de notas e, em seguida, copie os nomes de todas as VMs para as quais quer criar perfis para outro ficheiro (por exemplo, ProfileVMList.txt), com um nome de VM por linha. Este ficheiro é utilizado como entrada para o parâmetro *-VMListFile* da ferramenta de linha de comandos.

    ![Lista de nomes de VMs no Deployment Planner
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>Começar a criar perfis
Quando tiver a lista das VMs para as quais criar perfis, pode executar a ferramenta no modo de criação de perfis. Abaixo, pode ver a lista dos parâmetros obrigatórios e opcionais da ferramenta, para executá-la no modo de criação de perfis.

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Nome do parâmetro | Descrição |
|---|---|
| -Operation | StartProfiling |
| -Server | O nome de domínio completamente qualificado ou o endereço IP do vCenter Server/anfitrião ESXi do vSphere para cujas VMs vão ser criados perfis.|
| -User | O nome de utilizador para ligar ao vCenter Server/anfitrião ESXi do vSphere. O utilizador tem de ter, pelo menos, acesso só de leitura.|
| -VMListFile | O ficheiro que contém a lista de VMs para as quais criar perfis. O caminho do ficheiro pode ser absoluto ou relativo. Deve conter um nome/endereço IP de VM por linha. O nome da máquina virtual especificado no ficheiro deve ser igual ao nome da VM no vCenter Server/anfitrião ESXi do vSphere.<br>Por exemplo, o ficheiro VMList.txt contém as VMs seguintes:<ul><li>máquina_virtual_A</li><li>10.150.29.110</li><li>máquina_virtual_B</li><ul> |
|-NoOfMinutesToProfile|O número de minutos durante os quais a criação de perfis deve ser executada. O mínimo é de 30 minutos.|
|-NoOfHoursToProfile|O número de horas durante os quais a criação de perfis deve ser executada.|
| -NoOfDaysToProfile | O número de dias durante os quais a criação de perfis deve ser executada. Recomendamos executar a criação de perfis durante mais de sete dias, para garantir que o padrão da carga de trabalho no seu ambiente ao longo do período especificado é respeitado e utilizado para proporcionar uma recomendação precisa. |
|-Virtualization|Especifica o tipo de virtualização (VMware ou Hyper-V).|
| -Directory | (opcional) O caminho Universal Naming Convention (UNC) ou o caminho do diretório local para armazenar os dados de criação de perfis gerados durante a criação. Se não for indicado o nome de um diretório, será utilizado como diretório predefinido o diretório com o nome “ProfiledData” no caminho atual. |
| -Password | (Opcional) A palavra-passe a utilizar para ligar ao vCenter Server/anfitrião ESXi do vSphere. Se não indicar uma agora, ser-lhe-á pedida quando o comando for executado.|
|-Port|(Opcional) Número de porta para estabelecer ligação ao anfitrião do vCenter/ESXi. A porta predefinida é 443.|
|-Protocol| (Opcional) Protocolo especificado entre "http" ou "https" para ligar ao vCenter. O protocolo predefinido é https.|
| -StorageAccountName | (Opcional) O nome da conta de armazenamento utilizada para encontrar o débito alcançável para a replicação de dados no local para o Azure. Para calcular o débito, a ferramenta carrega dados de teste para esta conta de armazenamento. A conta de armazenamento tem de ser do tipo Fins gerais v1 (GPv1). |
| -StorageAccountKey | (Opcional) A chave da conta de armazenamento utilizada para aceder à mesma. Aceda a o portal do Azure > Contas de armazenamento > <*Nome da conta de armazenamento*> > Definições > Chaves de Acesso > Chave1. |
| -Ambiente | (opcional) Este é o seu ambiente da conta de Armazenamento do Azure de destino. Este pode ser um de três valores - AzureCloud, AzureUSGovernment, AzureChinaCloud. A predefinição é AzureCloud. Use o parâmetro quando a região de Azure alvo é ou O Governo dos EUA Azure ou Azure China 21Vianet. |


Recomendamos que crie perfis para VMs por mais que 7 dias. Se o volume de alterações alterar durante um mês, recomendamos que crie os perfis durante a semana que vir o volume de alterações máximo. A melhor forma consiste em criar perfis para 31 dias para obter a melhor recomendação. Durante o período de criação de perfis, ASRDeploymentPlanner.exe permanece em execução. A ferramenta aceita a entrada da duração da criação de perfis em dias. Para um teste rápido da ferramenta ou para uma prova de conceito pode criar perfis para algumas horas ou minutos. O período mínimo de criação de perfis são 30 minutos.

Durante a criação de perfis, pode, opcionalmente, transmitir o nome e a chave de uma conta de armazenamento para encontrar o débito que o Azure Site Recovery pode alcançar no momento da replicação do servidor de configuração ou do servidor de processos para o Azure. Se o nome e a chave da conta de armazenamento não forem transmitidos durante a criação de perfis, a ferramenta não calcula o débito alcançável.

Pode executar várias instâncias da ferramenta em vários conjuntos de VMs. Confirme que não existem nomes de VMs repetidos em nenhum dos conjuntos de criação de perfis. Por exemplo, se tiver criado perfis para dez VMs (VM1 a VM10) e, ao fim de alguns dias, quiser criar perfis para outras cinco (VM11 a VM15), pode executar a ferramenta noutra consola de linha de comandos para o segundo conjunto de VMs (VM11 a VM15). Assegure-se de que o segundo conjunto não tem nenhum nome de VMs da primeira instância de criação de perfis ou de que utiliza outro diretório de saída para a segunda execução. Se forem utilizadas duas instâncias da ferramenta para criar perfis para as mesmas VMs e o mesmo diretório de saída, o relatório gerado estará incorreto.

Por predefinição, a ferramenta está configurada para perfilar e gerar relatório até 1000 VMs. Pode mudar o limite, alterando o valor chave MaxVMsSupported no ficheiro *ASRDeploymentPlanner.exe.config*.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Com as predefinições, para criar um perfil de, por exemplo, 1500 VMs, crie dois ficheiros VMList.txt. Um com 1000 VMs e outro com uma lista de 500 VMs. Executar as duas instâncias do Azure Site Recovery Deployment Planner, uma com VMList1.txt e outra com VMList2.txt. Pode utilizar o mesmo caminho de diretório para armazenar os dados de criação de perfis das duas VMs VMList.

Constatámos que, com base na configuração de hardware, especialmente o tamanho de RAM do servidor a partir do qual a ferramenta é executada para gerar o relatório, a operação poderá falhar com memória insuficiente. Se o hardware for bom, pode alterar o MaxVMsSupported para qualquer valor superior.  

Se tiver vários servidores vCenter, terá de executar uma instância do ASRDeploymentPlanner para cada servidor vCenter para criação de perfis.

As configurações das VMs sã capturadas uma vez no início da operação de criação de perfis e armazenadas num ficheiro com o nome VMDetailList.xml. Estas informações são utilizadas quando o relatório é gerado. Não são capturas quaisquer alterações à configuração das VMs (por exemplo, um aumento no número de núcleos, discos ou NICs) desde o início até ao fim da criação de perfis. Caso a configuração de uma VM para a qual foram criados perfis tiver sofrido alterações durante a criação dos perfis, existe, na pré-visualização pública, uma solução para obter os últimos detalhes das VMs durante a geração do relatório:

* Criar uma cópia de segurança de VMdetailList.xml e eliminar o ficheiro da localização atual.
* Transmitir os argumentos -User e -Password no momento da geração de relatórios.

O comando de criação de perfis gera vários ficheiros no diretório de criação de perfis. Não elimine nenhum dos ficheiros, pois tal eliminação afeta a geração de relatórios.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Exemplo 1: criar perfis para VMs durante 30 dias e encontrar o débito no local para o Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Exemplo 2: criar perfis para VMs durante 15 dias

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Exemplo 3: criar perfis para VMs durante 60 minutos, para um breve teste à ferramenta
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>Exemplo 4: criar perfis para VMs durante 2 horas para uma prova de conceito
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* Se o servidor no qual a ferramenta está a ser executada for reiniciado ou falhar ou se utilizar Ctrl + C para fechar a ferramenta, os dados da criação de perfis são preservados. Contudo, esta situação pode levar à perda dos últimos 15 minutos de dados de criação de perfis. Nesse caso, terá de voltar a executar a ferramenta no modo de criação de perfis, quando o servidor for reiniciado.
>* Quando são transmitidos o nome e a chave da conta de armazenamento, a ferramenta mede o débito no último passo da criação de perfis. Se a ferramenta for fechada antes da conclusão da criação de perfis, o débito não é calculado. Para localizar o débito antes de gerar o relatório, pode executar a operação GetThroughput na consola da linha de comandos. Caso contrário, o relatório gerado não irá conter as informações de débito.


## <a name="generate-report"></a>Gerar o relatório
A ferramenta gera um ficheiro do Microsoft Excel com permissão para macros (ficheiro XLSM) como o resultado do relatório, que resume todas as recomendações de implementação. O relatório é nomeado `DeploymentPlannerReport_<unique numeric identifier>.xlsm` e colocado no diretório especificado.

>[!NOTE]
>A geração de relatórios requer um Windows PC ou Windows Server com o Excel 2013 ou mais tarde. O símbolo decimal desta máquina deve ser configurado como "." para produzir as estimativas de custos. No caso de ter configurado "" como símbolo decimal, por favor aceda a "Alterar os formatos de data, hora ou número" no Painel de Controlo e vá a "Definições Adicionais" para alterar o símbolo decimal para "."

Depois de concluída a criação de perfis, pode executar a ferramenta no modo de geração de relatórios. A tabela seguinte contém a lista dos parâmetros obrigatórios e opcionais da ferramenta, para executá-la no modo de geração de relatórios.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Nome do parâmetro | Descrição |
|-|-|
| -Operation | GenerateReport |
| -Server |  O nome de domínio completamente qualificado ou o endereço IP do vCenter/vSphere Server (utilize o mesmo nome ou endereço IP que utilizou no momento da criação de perfis) onde estão localizadas as VMs para as quais foram criados perfis e para as quais vão ser gerados relatórios. Tenha em atenção que, se tiver utilizado um vCenter Server no momento da criação de perfis, não pode utilizar um vSphere Server para a geração de relatórios e vice-versa.|
| -VMListFile | O ficheiro que contém a lista de VMs para as quais foram criados perfis e para as quais o relatório vai ser gerado. O caminho do ficheiro pode ser absoluto ou relativo. Deve conter um nome ou endereço IP de VM por linha. Os nomes das VMs especificados no ficheiro devem ser os mesmos que os nomes das VMs no vCenter Server/anfitrião ESXi do vSphere e corresponder ao que estava a ser utilizado no momento da criação de perfis.|
|-Virtualization|Especifica o tipo de virtualização (VMware ou Hyper-V).|
| -Directory | (Opcional) O caminho UNC ou o caminho do diretório local onde são armazenados os dados da criação de perfis (ficheiros gerados durante a criação de perfis). Estes dados são necessários para gerar o relatório. Se não for especificado um nome, será utilizado o diretório “ProfiledData”. |
| -GoalToCompleteIR | (Opcional) O número de horas em que a replicação inicial das VMs para as quais foram criados perfis tem de ser concluída. O relatório gerado mostra o número de VMs cuja replicação inicial pode ser concluída no tempo especificado. A predefinição são 72 horas. |
| -User | (Opcional) O nome de utilizador a utilizar para ligar ao vCenter/vSphere Server. É utilizado para obter as últimas informações de configuração das VMs, como o número de discos, núcleos e NICs, para utilizar no relatório. Se não for indicado o nome, são utilizadas as informações de configuração recolhidas no início da criação de perfis. |
| -Password | (Opcional) A palavra-passe a utilizar para ligar ao vCenter Server/anfitrião ESXi do vSphere. Se a palavra-passe não for indica como parâmetro, ser-lhe-á pedida mais tarde, quando o comando for executado. |
|-Port|(Opcional) Número de porta para estabelecer ligação ao anfitrião do vCenter/ESXi. A porta predefinida é 443.|
|-Protocol|(Opcional) Protocolo especificado entre "http" ou "https" para ligar ao vCenter. O protocolo predefinido é https.|
| -DesiredRPO | (Opcional) O objetivo de ponto de recuperação pretendido, em minutos. A predefinição são 15 minutos.|
| -Bandwidth | A largura de banda em Mbps. O parâmetro a utilizar para calcular o RPO que pode ser alcançado para a largura de banda especificada. |
| -StartDate | (Opcional) A data e hora de início, em MM-DD-AAAA:HH:MM (no formato de 24 horas). *StartDate* deve ser especificado juntamente com *o EndDate*. Se StartDate for especificado, o relatório será gerado para os dados de criação de perfis recolhidos entre StartDate e EndDate. |
| -EndDate | (Opcional) A data e hora de fim, em MM-DD-AAAA:HH:MM (no formato de 24 horas). *EndDate* deve ser especificado juntamente com *StartDate*. Se EndDate for especificado, o relatório será gerado para os dados de criação de perfis recolhidos entre StartDate e EndDate. |
| -GrowthFactor | (Opcional) O fator de crescimento, expresso em percentagem. A predefinição é 30 por cento. |
| -UseManagedDisks | (Opcional) UseManagedDisks - Sim/Não. A predefinição é Sim. O número de máquinas virtuais para a colocação de uma conta de armazenamento única ser calculada considerando se a Ativação pós-falha/Ativação pós-falha de Teste de máquinas virtuais é realizada no disco gerido, em vez do disco não gerido. |
|-SubscriptionId |(Opcional) A GUID da subscrição. Note que este parâmetro é necessário quando precisa de gerar o relatório de estimativa de custos com o preço mais recente baseado na sua subscrição, a oferta que está associada à sua subscrição e para a sua região Azure alvo específica na **moeda especificada.**|
|-TargetRegion|(Opcional) A região do Azure para onde está direcionada a replicação. Dado que o Azure tem custos diferentes por região, para gerar um relatório com uma região de destino do Azure específica, utilize este parâmetro.<br>A predefinição é WestUS2 ou a última região de destino utilizada.<br>Consulte a lista de [regiões de destino suportadas](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Opcional) A oferta associada à subscrição em questão. A Predefinição é MS-AZR-0003P (Pay As You Go).|
|-Currency|(Opcional) A moeda na qual os custos são mostrados no relatório gerado. A predefinição é o Dólar Norte-Americano ($) ou a última moeda utilizada.<br>Consulte a lista de [moedas suportadas](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).|

Por predefinição, a ferramenta está configurada para perfilar e gerar relatório até 1000 VMs. Pode mudar o limite, alterando o valor chave MaxVMsSupported no ficheiro *ASRDeploymentPlanner.exe.config*.
```xml
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Exemplo 1: gerar o relatório com valores predefinidos quando os dados de criação de perfis estão na unidade local
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Exemplo 2: gerar o relatório quando os dados de criação de perfis estão num servidor remoto
Deve ter acesso de leitura/escrita no diretório remoto.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Exemplo 3: gerar um relatório com largura de banda e objetivos específicos para concluir a replicação inicial no período especificado
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Exemplo 4: gerar um relatório com um fator de crescimento de 5 por cento em vez dos 30 por cento predefinidos
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Exemplo 5: gerar um relatório com um subconjunto de dados de criação de perfis
Por exemplo, tem 30 dias de dados de criação de perfis e quer gerar um relatório para apenas 20 dias.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Exemplo 6: gerar um relatório para um RPO de cinco minutos
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>Exemplo 7: gerar um relatório para a região do Azure do Sul da Índia com a Rúpia Indiana e uma ID de oferta específica

Note que o ID de subscrição é necessário para gerar relatório de custos numa moeda específica.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>Valor de percentil utilizado para o cálculo
**Que valor de percentil predefinido das métricas de desempenho recolhidas durante a criação de perfis é utilizado pela ferramenta no momento em que gera relatórios?**

A ferramenta é predefinida para os valores de percentil de 95 de IOPS de leitura/escrita, IOPS de escrita e alterações a dados recolhidas durante a criação de perfis para todas as VMs. Esta métrica garante que o pico de percentil 100 que as suas VMs poderão verificar devido a eventos temporários não é utilizado para determinar os requisitos de conta de armazenamento de destino e de largura de banda de origem. Por exemplo, um evento temporário pode consistir numa tarefa de cópia de segurança que é executada uma vez por dia, uma base de dados periódica a indexar ou uma atividade de geração de relatórios de atividades ou, ainda, outros eventos pontuais de curta duração semelhantes.

Utilizar valores de percentil 95 proporciona uma imagem verdadeira das características reais das cargas de trabalho e confere-lhe o melhor desempenho se essas cargas de trabalho forem executadas no Azure. Não prevemos que tenha de alterar este número. No entanto, se vier a alterá-lo (para o percentil 90, por exemplo), pode atualizar o ficheiro de configuração *ASRDeploymentPlanner.exe.config* na pasta predefinida e guardá-lo para gerar um relatório novo sobre os dados de criação de perfis existentes.
```xml
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Considerações sobre o fator de crescimento
**Por que motivo devo ter em conta o fator de crescimento quando planear implementações?**

É fundamental ter em conta o crescimento nas características da sua carga de trabalho, presumindo um potencial aumento na utilização ao longo do tempo. Depois de a proteção estar aplicada, caso as características da sua carga de trabalho sofram alterações, não pode mudar para outra conta de armazenamento para proteção sem desativar e reativar a proteção.

Por exemplo, digamos que, hoje, a sua VM se enquadra numa conta de replicação de armazenamento standard. Ao longo dos três próximos meses, é provável que ocorram várias alterações:

* O número de utilizadores da aplicação que é executada na VM vai aumentar.
* O aumento resultante nas alterações a dados na VM vai exigir que a VM passe para o armazenamento premium, para que a replicação do Site Recovery consiga manter o ritmo.
* Consequentemente, vai ter de desativar e reativar a proteção para uma conta de armazenamento premium.

Recomendamos vivamente que considere o crescimento durante o planeamento da implementação, cujo valor predefinido é 30 por cento. Você é o especialista no que diz respeito ao padrão de utilização da sua aplicação e às projeções de crescimento, pelo que pode alterar este número em conformidade quando gera um relatório. Além disso, pode gerar vários relatórios com diferentes fatores de crescimento com os mesmos dados de criação de perfis e determinar que recomendações de armazenamento de destino e de largura de banda de origem funcionam melhor para si.

O relatório do Microsoft Excel gerado contém as seguintes informações:

* [Resumo no local](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary)
* [Recomendações](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [VM<->Storage Placement (VM<->Colocação de Armazenamento)](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement)
* [Compatible VMs (VMs Compatíveis)](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms)
* [Incompatible VMs (VMs Não Compatíveis)](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms)
* [Estimativa de Custos](site-recovery-vmware-deployment-planner-cost-estimation.md)

![Deployment Planner](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>Obtenção de débito

Para estimar o débito que o Site Recovery pode alcançar no local para o Azure durante a replicação, execute a ferramenta no modo GetThroughput. A ferramenta calcula o débito do servidor no qual está a ser executada. Idealmente, este servidor baseia-se na orientação de tamanho do servidor de configuração. Se já tiver implementado componentes da infraestrutura do Site Recovery no local, execute a ferramenta no servidor de configuração.

Abra uma consola da linha de comandos e aceda à pasta da ferramenta Site Recovery Deployment Planner. Execute ASRDeploymentPlanner.exe com os parâmetros seguintes.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Nome do parâmetro | Descrição |
|-|-|
| -Operation | GetThroughput |
|-Virtualization|Especifica o tipo de virtualização (VMware ou Hyper-V).|
| -Directory | (Opcional) O caminho UNC ou o caminho do diretório local onde são armazenados os dados da criação de perfis (ficheiros gerados durante a criação de perfis). Estes dados são necessários para gerar o relatório. Se não for especificado o nome de um diretório, é utilizado o diretório “ProfiledData”. |
| -StorageAccountName | O nome da conta de armazenamento utilizado para encontrar a largura de banda consumida para a replicação dos dados no local para o Azure. Para calcular a largura de banda consumida, a ferramenta carrega dados de teste para esta conta de armazenamento. A conta de armazenamento tem de ser do tipo Fins gerais v1 (GPv1).|
| -StorageAccountKey | A chave da conta de armazenamento utilizada para aceder à mesma. Aceda ao portal do Azure > Contas de armazenamento > <*Nome da conta de armazenamento*> > Definições > Chaves de acesso > Chave1 (ou uma chave de acesso primário para contas de armazenamento clássicas). |
| -VMListFile | O ficheiro que contém a lista de VMs para as quais criar perfis para calcular a largura de banda consumida. O caminho do ficheiro pode ser absoluto ou relativo. Deve conter um nome/endereço IP de VM por linha. Os nomes das VMs especificados no ficheiro devem ser iguais aos nomes das VMs no vCenter Server/anfitrião ESXi do vSphere.<br>Por exemplo, o ficheiro VMList.txt contém as VMs seguintes:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Ambiente | (opcional) Este é o seu ambiente da conta de Armazenamento do Azure de destino. Este pode ser um de três valores - AzureCloud, AzureUSGovernment, AzureChinaCloud. A predefinição é AzureCloud. Use o parâmetro quando a região de Azure alvo é ou O Governo dos EUA Azure ou Azure China 21Vianet. |

A ferramenta cria vários ficheiros asrvhdfile<#>.vhd (em que # corresponde ao número de ficheiros) de 64 MB no diretório especificado. Para encontrar o débito, a ferramenta carrega os ficheiros para a conta de armazenamento. Depois de o débito ser medido, elimina todos os ficheiros da conta de armazenamento e do servidor local. Se a ferramenta for terminada por qualquer motivo enquanto está a calcular o débito, não elimina os ficheiros do armazenamento nem do servidor local. Tem de eliminá-los manualmente.

O débito é medido num determinado ponto no tempo e é o débito máximo que o Site Recovery pode alcançar durante a replicação, desde que todos os outros fatores permaneçam iguais. Por exemplo, se uma aplicação começar a consumir mais largura de banda na mesma rede, o débito real varia durante a replicação. Se estiver a executar o comando GetThroughput num servidor de configuração, a ferramenta não está ciente de nenhuma VM protegida nem da replicação em curso. O resultado do débito medido é diferente se a operação GetThroughput for executada quando as VMs protegidas têm elevadas alterações a dados. Recomendamos que execute a ferramenta em vários pontos no tempo durante a criação de perfis, para compreender que níveis de débito podem ser alcançados em vários momentos. No relatório, a ferramenta mostra o último débito medido.

### <a name="example"></a>Exemplo
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -Virtualization VMware -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Execute a ferramenta num servidor que tenha as mesmas características de armazenamento e CPU que o servidor de configuração.
>
> Para a replicação, defina a largura de banda recomendada para satisfazer o RPO durante 100 por cento do tempo. Se depois de definir a largura de banda certa, não vir um aumento no débito alcançado comunicado pela ferramenta, faça o seguinte:
>
>  1. Verifique se existe algum Quality of Service (QoS) de rede que esteja a limitar o débito do Site Recovery.
>
>  2. Verifique se o cofre do Site Recovery está na região física suportada mais próxima do Microsoft Azure, para minimizar a latência de rede.
>
>  3. Verifique as características do seu armazenamento local para determinar se pode melhorar o hardware (por exemplo, passar de HDD para SSD).
>
>  4. Altere as definições do Site Recovery no servidor de processos, para [aumentar a quantidade de largura de banda utilizada para a replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="next-steps"></a>Passos seguintes
* [Analise o relatório gerado.](site-recovery-vmware-deployment-planner-analyze-report.md)
