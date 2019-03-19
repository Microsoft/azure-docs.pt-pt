---
title: Executar o Azure Site Recovery Deployment Planner para recuperação após desastre de Hyper-V para o Azure | Documentos da Microsoft
description: Este artigo descreve como executar o Azure Site Recovery Deployment Planner para recuperação após desastre de Hyper-V para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 776523bb001848e6ecc153f670a96e3143e2ac0d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006345"
---
# <a name="run-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Executar o planeador de implementação do Azure Site Recovery para recuperação após desastre de Hyper-V para o Azure

Pode executar a recuperação de Site ferramenta Planeador de implementações da linha de comandos (ASRDeploymentPlanner.exe) em qualquer um desses quatro modos: 
-   Obter a lista de máquina virtual (VM)
-   [Perfil](#profile-hyper-v-vms)
-   Gerar um relatório
-   [Obtenção de débito](#get-throughput)

Primeiro, execute a ferramenta para obter a lista das VMs de um ou vários anfitriões Hyper-V. Então, execute a ferramenta no modo de criação de perfis, para recolher IOPS e alterações a dados da VM. Em seguida, execute-a para gerar o relatório de modo a localizar os requisitos de largura de banda de rede e de armazenamento.

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>Obter a lista de VMs para a criação de perfis de VMs do Hyper-V
Em primeiro lugar, precisa de uma lista das VMs para as quais vão ser criados perfis. Utilize o modo GetVMList da ferramenta de planeamento de implementações para gerar a lista de VMs presentes em vários anfitriões do Hyper-V, com num único comando. Depois de gerar a lista completa, pode remover as VMs cujo perfil não quer criar do ficheiro de saída. Em seguida, utilize o ficheiro de saída para todas as outras operações: criação de perfis, geração de relatórios e obtenção do débito.

Pode gerar a lista de VMs ao apontar a ferramenta para um cluster Hyper-V ou um anfitrião Hyper-V autónomo, ou uma combinação dos dois.

### <a name="command-line-parameters"></a>Parâmetros da linha de comandos
A tabela seguinte contém a lista dos parâmetros obrigatórios e opcionais da ferramenta para executar no modo GetVMList. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```

| Nome do parâmetro | Descrição |
|---|---|
| -Operation | GetVMList |
| -User | O nome de utilizador para ligar ao anfitrião Hyper-V ou cluster Hyper-V. O utilizador tem de ter acesso administrativo.|
| -ServerListFile | O ficheiro com a lista de servidores que contém as VMs das quais criar o perfil. O caminho do ficheiro pode ser absoluto ou relativo. Este ficheiro deve conter um dos seguintes em cada linha:<ul><li>Nome de anfitrião do Hyper-V ou endereço IP</li><li>Nome de cluster do Hyper-V ou endereço IP</li></ul><br>**Exemplo:** Ficheiro ServerList. txt contém os seguintes servidores:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(Opcional) A convenção de nomenclatura universal (UNC) ou o caminho do diretório local para armazenar os dados gerados durante esta operação. Se não for especificado um nome, o diretório com o nome ProfiledData no caminho atual é utilizado como diretório predefinido.|
|-OutputFile| (Opcional) O ficheiro com a lista de VMs obtida dos servidores de Hyper-V é guardado. Se um nome não for mencionado, os detalhes são armazenados em VMList.txt.  Utilize o ficheiro para iniciar a criação de perfis depois de remover as VMs que não precisam de ter o perfil criado.|
|-Password|(Opcional) A palavra-passe para ligar ao anfitrião Hyper-V. Se não o especificar como um parâmetro, ser-lhe-á solicitado quando executar o comando.|

### <a name="getvmlist-discovery"></a>Deteção de GetVMList

- **Cluster Hyper-V**: Quando o nome do cluster Hyper-V é fornecido no ficheiro de lista do servidor, a ferramenta encontra todos os nós de Hyper-V do cluster e obtém as VMs presentes em cada dos anfitriões do Hyper-V.
**Anfitrião Hyper-V**: Quando recebe o nome de anfitrião do Hyper-V, a ferramenta verifica primeiro se pertence a um cluster. Se sim, a ferramenta obtém os nós que pertencem ao cluster. Em seguida, obtém as VMs de cada anfitrião do Hyper-V. 

Também pode escolher listar, num ficheiro, os nomes amigáveis ou os endereços IP das VMs para as quais quer criar perfis manualmente.

Abra o ficheiro de saída no Bloco de notas e, em seguida, copie os nomes de todas as VMs para as quais quer criar perfis para outro ficheiro (por exemplo, ProfileVMList.txt). Utilize um nome de VM por linha. Este ficheiro é utilizado como entrada para o parâmetro -VMListFile da ferramenta para todas as outras operações: criação de perfis, geração de relatórios e obtenção de débito.

### <a name="examples"></a>Exemplos

#### <a name="store-the-list-of-vms-in-a-file"></a>Armazenar a lista de VMs num ficheiro
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>Armazenar a lista de VMs na localização predefinida (-Directory path)
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Criar perfil de VMs do Hyper-V
No modo de criação de perfis, a ferramenta Deployment Planner liga-se a cada dos anfitriões do Hyper-V para recolher dados de desempenho da VM. 

A criação de perfis não afeta o desempenho das VMs de produção, porque não é feita nenhuma ligação direta para as mesmas. Todos os dados de desempenho são recolhidos a partir do anfitrião do Hyper-V.

A ferramenta consulta o anfitrião Hyper-V uma vez a cada 15 segundos para garantir a exatidão da criação de perfis. Armazena a média dos dados do contador de desempenho de cada minuto.

A ferramenta processa de forma totalmente integrada a migração de VMs de um nó para outro no cluster e a migração de armazenamento dentro de um anfitrião.

### <a name="getting-the-vm-list-to-profile"></a>Obter a lista de VMs para as quais criar perfis
Para criar uma lista de VMs para o perfil, consulte a operação GetVMList.

Quando tiver a lista das VMs para as quais criar perfis, pode executar a ferramenta no modo de criação de perfis. 

### <a name="command-line-parameters"></a>Parâmetros da linha de comandos
A tabela seguinte lista os parâmetros obrigatórios e opcionais da ferramenta para executar no modo de criação de perfis. A ferramenta é comum para cenários de passagem do VMware para o Azure e do Hyper-V para o Azure. Estes parâmetros são aplicáveis ao Hyper-V. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Nome do parâmetro | Descrição |
|---|---|
| -Operation | StartProfiling |
| -User | O nome de utilizador para ligar ao anfitrião Hyper-V ou cluster Hyper-V. O utilizador tem de ter acesso administrativo.|
| -VMListFile | O ficheiro com a lista de VMs para as quais criar perfis. O caminho do ficheiro pode ser absoluto ou relativo. Para o Hyper-V, este ficheiro é o ficheiro de saída da operação GetVMList. Se estiver a preparar manualmente, o ficheiro deve conter um nome de servidor ou endereço IP seguido do nome da VM (separado com uma \ por linha). O nome da VM especificada no ficheiro deve ser igual ao nome da VM no anfitrião Hyper-V.<br><br>**Exemplo:** Vmlist. txt contém as VMs seguintes:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|O número de minutos durante os quais a criação de perfis será executada. O mínimo é de 30 minutos.|
|-NoOfHoursToProfile|O número de horas durante os quais a criação de perfis será executada.|
|-NoOfDaysToProfile |O número de dias durante os quais a criação de perfis será executada. Recomendamos que execute a criação de perfis por mais de 7 dias. Esta duração ajuda a garantir que o padrão da carga de trabalho no seu ambiente ao longo do período especificado é respeitado e utilizado para proporcionar uma recomendação precisa.|
|-Virtualization|O tipo de virtualização (VMware ou Hyper-V).|
|-Directory|(Opcional) O UNC ou caminho de diretório local para armazenar os dados de criação de perfis gerados durante a criação. Se não for especificado um nome, será utilizado como diretório predefinido o diretório com o nome ProfiledData no caminho atual.|
|-Password|(Opcional) A palavra-passe para ligar ao anfitrião Hyper-V. Se não o especificar como um parâmetro, ser-lhe-á solicitado quando executar o comando.|
|-StorageAccountName|(Opcional) O nome da conta de armazenamento utilizada para encontrar o débito alcançável para a replicação de dados no local para o Azure. Para calcular o débito, a ferramenta carrega dados de teste para esta conta de armazenamento. A conta de armazenamento tem de ser do tipo Fins gerais v1 (GPv1).|
|-StorageAccountKey|(Opcional) A chave que é utilizada para aceder à conta de armazenamento. Aceda ao portal do Azure > **Contas de armazenamento** > *nome da conta de armazenamento* > **Definições** > **Chaves de Acesso** > **Chave1** (ou a chave de acesso primária de uma conta de armazenamento clássica).|
|-Ambiente|(Optional) O seu ambiente de destino para a conta de armazenamento do Azure. Pode ser um de três valores: AzureCloud, AzureUSGovernment ou AzureChinaCloud. A predefinição é AzureCloud. Utilize o parâmetro quando a região de destino está na cloud do Azure US Government ou do Azure China.|

Recomendamos que crie perfis para VMs por mais que 7 dias. Se o volume de alterações mudar mensalmente, recomendamos que crie os perfis durante a semana em que constatar o volume de alterações máximo. A melhor forma consiste em criar perfis para 31 dias, de modo a obter a melhor recomendação. 

Durante o período de criação de perfis, ASRDeploymentPlanner.exe permanece em execução. A ferramenta aceita a entrada da duração da criação de perfis em dias. Para um teste rápido da ferramenta ou para uma prova de conceito, pode criar perfis para algumas horas ou minutos. O período mínimo de criação de perfis são 30 minutos. 

Durante a criação de perfis, pode, opcionalmente, transmitir o nome e a chave de uma conta de armazenamento para encontrar o débito que o Azure Site Recovery pode alcançar no momento da replicação do servidor de Hyper-V para o Azure. Se o nome e a chave da conta de armazenamento não forem transmitidos durante a criação de perfis, a ferramenta não calcula o débito alcançável.

Pode executar várias instâncias da ferramenta em vários conjuntos de VMs. Confirme que não existem nomes de VMs repetidos em nenhum dos conjuntos de criação de perfis. Por exemplo, vamos supor que criou perfis para 10 VMs (da VM1 à VM10). Após alguns dias, pretende criar perfis de mais 5 VMs (da VM11 à VM15). Pode executar a ferramenta a partir de outra consola da linha de comandos para o segundo conjunto de VMs (da VM11 à VM15). 

Certifique-se de que o segundo conjunto de VMs não inclui nenhum nome de VM da primeira instância de criação de perfis ou de que utiliza outro diretório de saída para a segunda execução. Se forem utilizadas duas instâncias da ferramenta para criar perfis para as mesmas VMs e o mesmo diretório de saída, o relatório gerado estará incorreto. 

Por predefinição, a ferramenta está configurada para criar perfis e gerar relatórios de até 1000 VMs. Pode mudar o limite, alterando o valor chave MaxVMsSupported no ficheiro ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Com as predefinições, para criar um perfil (por exemplo) de 1500 VMs, crie dois ficheiros VMList.txt. Um ficheiro tem 1000 VMs, e o outro tem 500 VMs. Execute as duas instâncias do planeador de implementações do Azure Site Recovery, uma com o VMList1.txt e outra com o VMList2.txt. Pode utilizar o mesmo caminho de diretório para armazenar os dados de criação de perfis das duas VMs VMList. 

Com base na configuração de hardware (especialmente o tamanho de RAM) do servidor a partir do qual a ferramenta é executada para gerar o relatório, a operação poderá falhar com memória insuficiente. Se o hardware for bom, pode alterar o MaxVMsSupported para qualquer valor superior.  

As configurações das VMs sã capturadas uma vez no início da operação de criação de perfis e armazenadas num ficheiro com o nome VMDetailList.xml. Estas informações são utilizadas quando o relatório é gerado. Não são capturas quaisquer alterações à configuração das VMs (por exemplo, um aumento no número de núcleos, discos ou NICs) desde o início até ao fim da criação de perfis. Caso a configuração de uma VM para a qual foram criados perfis tenha sofrido alterações durante a criação dos perfis, eis uma solução para obter os detalhes mais recentes da VM durante a geração do relatório:

* Criar uma cópia de segurança de VMdetailList.xml e eliminar o ficheiro da localização atual.
* Transmitir os argumentos -User e -Password no momento da geração de relatórios.

O comando de criação de perfis gera vários ficheiros no diretório de criação de perfis. Não elimine nenhum dos ficheiros, pois tal eliminação afeta a geração de relatórios.

### <a name="examples"></a>Exemplos

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Criar perfis de VMs durante 30 dias e encontrar o débito no local para o Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>Criar perfis de VMs durante 15 dias
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\vCenter1_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Criar perfis de VMs durante 60 minutos, para um breve teste à ferramenta
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>Criar perfis de VMs durante 2 horas para uma prova de conceito
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>Considerações de criação de perfis

Se o servidor no qual a ferramenta está a ser executada for reiniciado ou falhar, ou se utilizar Ctrl+C para fechar a ferramenta, os dados da criação de perfis são preservados. Contudo, esta situação pode levar à perda dos últimos 15 minutos de dados de criação de perfis. Nesses casos, terá de voltar a executar a ferramenta no modo de criação de perfis, quando o servidor for reiniciado.

Quando são transmitidos o nome e a chave da conta de armazenamento, a ferramenta mede o débito no último passo da criação de perfis. Se a ferramenta for fechada antes da conclusão da criação de perfis, o débito não é calculado. Para localizar o débito antes de gerar o relatório, pode executar a operação GetThroughput na consola da linha de comandos. Caso contrário, o relatório gerado não irá conter as informações de débito.

O Azure Site Recovery não suporta VMs com discos iSCSI e pass-through. A ferramenta não é possível detetar e criar perfis iSCSI e pass-through discos anexados a VMs.

## <a name="generate-a-report"></a>Gerar um relatório
A ferramenta gera um ficheiro do Microsoft Excel com permissão para macros (ficheiro XLSM) como o resultado do relatório. Resume todas as recomendações de implementação. O nome do relatório é DeploymentPlannerReport_*identificador numérico exclusivo*.xlsm, que é colocado no diretório especificado.

Depois de concluída a criação de perfis, pode executar a ferramenta no modo de geração de relatórios. 

### <a name="command-line-parameters"></a>Parâmetros da linha de comandos
A tabela seguinte contém a lista dos parâmetros obrigatórios e opcionais da ferramenta, para executá-la no modo de geração de relatórios. A ferramenta é comum para a passagem do VMware para o Azure e do Hyper-V para o Azure. Os seguintes parâmetros são aplicáveis ao Hyper-V.
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```

| Nome do parâmetro | Descrição |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | O ficheiro que contém a lista de VMs para as quais foram criados perfis e para as quais o relatório será gerado. O caminho do ficheiro pode ser absoluto ou relativo. Para o Hyper-V, este ficheiro é o ficheiro de saída da operação GetVMList. Se estiver a preparar manualmente, o ficheiro deve conter um nome de servidor ou endereço IP seguido do nome da VM (separado com uma \ por linha). O nome da VM especificada no ficheiro deve ser igual ao nome da VM no anfitrião Hyper-V.<br><br>**Exemplo:** Vmlist. txt contém as VMs seguintes:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|O tipo de virtualização (VMware ou Hyper-V).|
|-Directory|(Opcional) O caminho UNC ou o caminho do diretório local onde são armazenados os dados da criação de perfis (ficheiros gerados durante a criação de perfis). Estes dados são necessários para gerar o relatório. Se não for especificado um nome, será utilizado como diretório predefinido o diretório com o nome ProfiledData no caminho atual.|
| -User | (Opcional) O nome de utilizador para ligar ao anfitrião Hyper-V ou cluster Hyper-V. O utilizador tem de ter acesso administrativo. O utilizador e a palavra-passe são utilizados para obter as últimas informações de configuração das VMs (como o número de discos, núcleos e NICs) a utilizar no relatório. Se este valor não for indicado, são utilizadas as informações de configuração recolhidas durante a criação de perfis.|
|-Password|(Opcional) A palavra-passe para ligar ao anfitrião Hyper-V. Se não o especificar como um parâmetro, ser-lhe-á solicitado quando executar o comando.|
| -DesiredRPO | (Opcional) O objetivo de ponto de recuperação (RPO) pretendido, em minutos. A predefinição são 15 minutos.|
| -Bandwidth | (Opcional) A largura de banda em megabits por segundo. Utilize este parâmetro para calcular o RPO que pode ser alcançado para a largura de banda especificada. |
| -StartDate | (Opcional) A data e hora de início, no formato MM-DD-AAAA:HH:MM (24 horas). StartDate tem de ser especificado juntamente com EndDate. Se StartDate for especificado, o relatório será gerado para os dados de criação de perfis recolhidos entre StartDate e EndDate. |
| -EndDate | (Opcional) A data e hora de fim, no formato MM-DD-AAAA:HH:MM (24 horas). EndDate tem de ser especificado juntamente com StartDate. Se EndDate for especificado, o relatório será gerado para os dados de criação de perfis recolhidos entre StartDate e EndDate. |
| -GrowthFactor | (Opcional) O fator de crescimento, expresso em percentagem. A predefinição é 30 por cento. |
| -UseManagedDisks | (Opcional) UseManagedDisks: Sim/não. A predefinição é Sim. O número de máquinas virtuais que podem ser colocadas numa única conta de armazenamento é calculado consoante a ativação pós-falha/ativação pós-falha de teste de máquinas virtuais seja realizada num disco gerido, em vez de num disco não gerido. |
|-SubscriptionId |(Opcional) A GUID da subscrição. Utilize este parâmetro para gerar o relatório de estimativa de custos com o preço mais recente com base na sua subscrição, na oferta que está associada à sua subscrição e na região de destino do Azure na moeda especificada.|
|-TargetRegion|(Opcional) A região do Azure para onde está direcionada a replicação. Dado que o Azure tem custos diferentes por região, para gerar um relatório com uma região de destino do Azure específica, utilize este parâmetro. A predefinição é euaoeste2 ou a última região de destino utilizada. Consulte a lista de [regiões de destino suportadas](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Opcional) A oferta associada à subscrição. A Predefinição é MS-AZR-0003P (Pay As You Go).|
|-Currency|(Opcional) A moeda na qual os custos são mostrados no relatório gerado. A predefinição é o Dólar Norte-Americano ($) ou a última moeda utilizada. Consulte a lista de [moedas suportadas](hyper-v-deployment-planner-cost-estimation.md#supported-currencies).|

Por predefinição, a ferramenta está configurada para criar perfis e gerar relatórios de até 1000 VMs. Pode mudar o limite, alterando o valor chave MaxVMsSupported no ficheiro ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>Exemplos
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Gerar um relatório com valores predefinidos quando os dados de criação de perfis estão na unidade local
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Gerar um relatório quando os dados de criação de perfis estão num servidor remoto
Deve ter acesso de leitura/escrita no diretório remoto.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "\\PS1-W2K12R2\Hyper-V_ProfiledData" -VMListFile "\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>Gerar um relatório com uma largura de banda específica que vai aprovisionar para a replicação
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Gerar um relatório com um fator de crescimento de 5 por cento, em vez dos 30 por cento predefinidos 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>Gerar um relatório com um subconjunto de dados de criação de perfis
Por exemplo, tem 30 dias de dados de criação de perfis e quer gerar um relatório para apenas 20 dias.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>Gerar um relatório para um RPO de cinco minutos
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>Gerar um relatório para a região do Azure do Sul da Índia com a Rúpia Indiana e um ID de oferta específico
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>Valor de percentil utilizado para o cálculo
Quando a ferramenta gera um relatório, o valor de percentil 95 é assumido como predefinição para IOPS de leitura/escrita, IPOS de escrita e alterações de dados. Estes valores são recolhidos durante a criação de perfis de todas as VMs. Esta métrica garante que o pico de percentil 100 que as suas VMs poderão constatar devido a eventos temporários não é utilizado para determinar os requisitos de conta de armazenamento de destino e de largura de banda de origem. Por exemplo, um evento temporário pode consistir numa tarefa de cópia de segurança que é executada uma vez por dia, uma indexação periódica de base de dados ou uma atividade de geração de relatórios de análise ou outro evento pontual de curta duração.

Utilizar um valor de percentil 95 proporciona uma imagem verdadeira das características reais das cargas de trabalho e confere-lhe o melhor desempenho, se essas cargas de trabalho forem executadas no Azure. Não prevemos que tenha de alterar este número. No entanto, se vier a alterá-lo (para um percentil 90, por exemplo), pode atualizar o ficheiro de configuração ASRDeploymentPlanner.exe.config na pasta predefinida e guardá-lo para gerar um relatório novo sobre os dados de criação de perfis existentes.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>Considerações de fator de crescimento
É fundamental ter em conta o crescimento nas características da sua carga de trabalho, presumindo um potencial aumento na utilização ao longo do tempo. Depois de a proteção estar aplicada, caso as características da sua carga de trabalho sofram alterações, não pode mudar para outra conta de armazenamento para proteção sem desativar e reativar a proteção.

Por exemplo, digamos que, hoje, a sua VM se enquadra numa conta de replicação de armazenamento standard. Ao longo dos três próximos meses, é provável que ocorram estas alterações:

1. O número de utilizadores da aplicação que é executada na VM vai aumentar.
2. O aumento das alterações a dados na VM vai exigir que a VM passe para o armazenamento premium, para que a replicação do Azure Site Recovery consiga manter o ritmo.
3. Vai ter de desativar e reativar a proteção para uma conta de armazenamento premium.

Recomendamos vivamente que considere o crescimento durante o planeamento da implementação. Embora o valor predefinido seja de 30 por cento, você é o especialista no que diz respeito ao padrão de utilização da sua aplicação é às projeções de crescimento. Pode alterar este número em conformidade quando gera um relatório. Além disso, pode gerar múltiplos relatórios com vários fatores de crescimento com os mesmos dados de criação de perfis. Pode então determinar quais as recomendações de armazenamento de destino e largura de banda de origem mais adequadas para si. 

O relatório do Microsoft Excel gerado contém as seguintes informações:

* [Resumo no local](hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Recommendations (Recomendações)](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [VM-colocação em armazenamento](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [Compatible VMs (VMs Compatíveis)](hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [Incompatible VMs (VMs Não Compatíveis)](hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [Requisito de armazenamento no local](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [Lotes de IR](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Estimativa de custos](hyper-v-deployment-planner-cost-estimation.md)

![Relatório do planeador de implementação](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Obtenção de débito
Para estimar o débito que o Azure Site Recovery pode alcançar no local para o Azure durante a replicação, execute a ferramenta no modo GetThroughput. A ferramenta calcula o débito do servidor no qual está a ser executada. Idealmente, este servidor é o servidor Hyper-V cujas VMs vão ser protegidas. 

### <a name="command-line-parameters"></a>Parâmetros da linha de comandos 
Abra uma consola da linha de comandos e aceda à pasta da ferramenta de planeamento de implementações do Azure Site Recovery. Execute ASRDeploymentPlanner.exe com os parâmetros seguintes.
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```

 Nome do parâmetro | Descrição |
|---|---|
| -Operation | GetThroughput |
|-Virtualization|O tipo de virtualização (VMware ou Hyper-V).|
|-Directory|(Opcional) O caminho UNC ou o caminho do diretório local onde são armazenados os dados da criação de perfis (ficheiros gerados durante a criação de perfis). Estes dados são necessários para gerar o relatório. Se não for especificado um nome, será utilizado como diretório predefinido o diretório com o nome ProfiledData no caminho atual.|
| -StorageAccountName | O nome da conta de armazenamento utilizado para encontrar a largura de banda consumida para a replicação dos dados no local para o Azure. Para calcular a largura de banda consumida, a ferramenta carrega dados de teste para esta conta de armazenamento. A conta de armazenamento tem de ser do tipo Fins gerais v1 (GPv1).|
| -StorageAccountKey | A chave da conta de armazenamento utilizada para aceder à mesma. Aceda ao portal do Azure > **Contas de armazenamento** > *nome da conta de armazenamento* > **Definições** > **Chaves de Acesso** > **Chave1**.|
| -VMListFile | O ficheiro que contém a lista de VMs para as quais criar perfis para calcular a largura de banda consumida. O caminho do ficheiro pode ser absoluto ou relativo. Para o Hyper-V, este ficheiro é o ficheiro de saída da operação GetVMList. Se estiver a preparar manualmente, o ficheiro deve conter um nome de servidor ou endereço IP seguido do nome da VM (separado com uma \ por linha). O nome da VM especificada no ficheiro deve ser igual ao nome da VM no anfitrião Hyper-V.<br><br>**Exemplo:** Vmlist. txt contém as VMs seguintes:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Ambiente|(Optional) O seu ambiente de destino para a conta de armazenamento do Azure. Pode ser um de três valores: AzureCloud, AzureUSGovernment ou AzureChinaCloud. A predefinição é AzureCloud. Utilize o parâmetro quando a região do Azure de destino for Azure US Government ou Azure China.|

### <a name="example"></a>Exemplo
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>Considerações de débito

A ferramenta cria vários ficheiros asrvhdfile*número*.vhd (em que o *número* corresponde ao número de ficheiros) de 64 MB no diretório especificado. Para encontrar o débito, a ferramenta carrega os ficheiros para a conta de armazenamento. Depois de o débito ser medido, elimina todos os ficheiros da conta de armazenamento e do servidor local. Se a ferramenta for terminada por qualquer motivo enquanto está a calcular o débito, não elimina os ficheiros da conta de armazenamento nem do servidor local. Tem de eliminá-los manualmente.

O débito é medido num ponto no tempo especificado. É o débito máximo que o Azure Site Recovery pode alcançar durante a replicação, se todos os outros fatores permanecerem os mesmos. Por exemplo, se uma aplicação começar a consumir mais largura de banda na mesma rede, o débito real varia durante a replicação. O resultado do débito medido é diferente se a operação GetThroughput for executada quando as VMs protegidas têm elevadas alterações a dados. 

Para compreender que níveis de débito podem ser alcançados em vários momentos, recomendamos que execute a ferramenta em vários pontos no tempo durante a criação de perfis. No relatório, a ferramenta mostra o último débito medido.

> [!NOTE]
> Execute a ferramenta num servidor que tenha as mesmas características de armazenamento e CPU de um servidor Hyper-V.

Para a replicação, defina a largura de banda recomendada para satisfazer o RPO durante 100 por cento do tempo. Se depois de definir a largura de banda certa, não vir um aumento no débito alcançado comunicado pela ferramenta, faça o seguinte:

1. Verifique se existe algum problema de Quality of Service (QoS) de rede que esteja a limitar o débito do Azure Site Recovery.
2. Verifique se o cofre do Azure Site Recovery está na região física suportada mais próxima do Microsoft Azure, para minimizar a latência de rede.
3. Verifique as características do seu armazenamento local para determinar se pode melhorar o hardware (por exemplo, passar de HDD para SSD).

    
## <a name="next-steps"></a>Passos Seguintes
* [Analisar o relatório gerado](hyper-v-deployment-planner-analyze-report.md)
