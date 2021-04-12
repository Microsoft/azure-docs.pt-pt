---
title: Problemas de replicação na migração de VMware VMware sem agente
description: Obtenha ajuda com falhas do ciclo de replicação
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: troubleshooting
ms.date: 08/17/2020
ms.openlocfilehash: 5487e51ad73ab903e7b61de266e2c28d282a56c1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568663"
---
# <a name="troubleshooting-replication-issues-in-agentless-vmware-vm-migration"></a>Problemas de resolução de problemas na migração VMware VM sem agente

Este artigo descreve algumas questões comuns e erros específicos que poderá encontrar quando replicar VMware no local usando o método Azure Migrate: Server Migration agentless.

Quando replica uma máquina virtual VMware utilizando o método de replicação sem agente, os dados da máquina virtual&#39;discos (vmdks) são replicados para replicar discos geridos por réplicas na sua subscrição Azure. Quando a replicação começa para um VM, ocorre um ciclo inicial de replicação no qual cópias completas dos discos são replicadas. Após a replicação inicial concluída, os ciclos de replicação incrementais são programados periodicamente para transferir quaisquer alterações que tenham ocorrido desde o ciclo de replicação anterior.

Pode ocasionalmente ver ciclos de replicação falhando para um VM. Estas falhas podem ocorrer devido a razões que vão desde problemas na configuração da rede no local até problemas no backend do Azure Migrate Cloud Service. Neste artigo, iremos:

 - Mostrar-lhe como pode monitorizar o estado de replicação e resolver erros.
 - Enunciar alguns dos erros de replicação que ocorrem comumente e sugerir medidas adicionais para remediar os mesmos.

## <a name="monitor-replication-status-using-the-azure-portal"></a>Monitorize o estado de replicação utilizando o portal Azure

Utilize os seguintes passos para monitorizar o estado de replicação das suas máquinas virtuais:

  1. Aceda à página de Servidores em Azure Migrate no portal Azure.
  ![Imagem 1](./media/troubleshoot-changed-block-tracking-replication/image0.png)
  1. Navegue para a página "Máquinas de replicação" clicando em "Replicating servers" no azulejo de migração do servidor.
  ![Imagem 2](./media/troubleshoot-changed-block-tracking-replication/image1.png)
  1. Você verá uma lista de servidores replicando juntamente com informações adicionais como estado, saúde, última hora de sincronização, etc. A coluna de saúde indica a saúde de replicação atual do VM. Um valor 'Crítico' ou 'Aviso' na coluna de saúde normalmente indica que o ciclo de replicação anterior para o VM falhou. Para obter mais detalhes, clique com o botão direito no VM e selecione "Detalhes de erro". A página Error Details contém informações sobre o erro e detalhes adicionais sobre como resolver problemas. Você também verá um link "Eventos Recentes" que pode ser usado para navegar na página de eventos para o VM.
  ![Imagem 3](./media/troubleshoot-changed-block-tracking-replication/image2.png)
  1. Clique em "Eventos Recentes" para ver as falhas do ciclo de replicação anteriores para o VM. Na página de eventos, procure o mais recente evento do tipo "Ciclo de replicação falhou" ou "Ciclo de replicação falhou para o disco" para o VM.
  ![Imagem 4](./media/troubleshoot-changed-block-tracking-replication/image3.png)
  1. Clique no evento para entender as possíveis causas do erro e recomendou medidas de reparação. Utilize as informações fornecidas para resolver problemas e remediar o erro.
 ![Imagem 5](./media/troubleshoot-changed-block-tracking-replication/image4.png)

## <a name="common-replication-errors"></a>Erros de replicação comuns

Esta secção descreve alguns dos erros comuns e como pode resolvê-los.

## <a name="key-vault-operation-failed-error-when-trying-to-replicate-vms"></a>A operação do Cofre de Chaves falhou ao tentar replicar VMs

**Erro:** "A operação do Cofre de Chaves falhou. Operação : Configure conta de armazenamento gerido, Cofre chave: nome do cofre-chave, Conta de armazenamento: nome da conta de armazenamento falhou com o erro:"

**Erro:** "A operação do Cofre de Chaves falhou. Operação : Gerar definição de assinatura de acesso partilhado, Cofre-chave: nome do cofre-chave, Conta de Armazenamento: nome da conta de armazenamento falhou com o erro:"

![Key Vault](./media/troubleshoot-changed-block-tracking-replication/key-vault.png)

Este erro ocorre normalmente porque a Política de Acesso ao Utilizador para o Cofre de Chaves não dá ao utilizador atualmente registado as permissões necessárias para configurar contas de armazenamento para serem geridas pelo Key Vault. Para verificar a política de acesso do utilizador no cofre de chaves, aceda à página do cofre key no portal para o cofre chave e selecione as políticas de acesso 

Quando o portal cria o cofre de chaves, também adiciona uma política de acesso ao utilizador que concede as permissões de utilizador atualmente registadas para configurar contas de armazenamento para serem geridas por Key Vault. Isto pode falhar por duas razões

- O utilizador registado no sessão é um principal remoto no cliente Azure inquilino (subscrição CSP - e o utilizador registado no utilizador é o administrador do parceiro). A solução neste caso é eliminar o cofre da chave, sair do portal e, em seguida, iniciar sessão com uma conta de utilizador do cliente inquilino (não um principal remoto) e voltar a tentar a operação. O parceiro CSP terá normalmente uma conta de utilizador nos clientes Azure Ative Directory inquilino que podem usar. Caso contrário, podem criar uma nova conta de utilizador para si próprios nos clientes Azure Ative Directory inquilino, fazer login no portal como novo utilizador e, em seguida, voltar a tentar a operação de replicação. A conta utilizada deve ter permissões de Administrador de Acesso ao Proprietário ou ao Utilizador concedidos à conta no grupo de recursos (grupo de recursos do projeto Migrate)

- O outro caso em que isto pode acontecer é quando um utilizador (user1) tentou configurar a replicação inicialmente e encontrou uma falha, mas o cofre-chave já foi criado (e a política de acesso ao utilizador adequadamente atribuída a este utilizador). Agora, num ponto posterior, um utilizador diferente (user2) tenta configurar a replicação, mas a conta de armazenamento gerido configure ou a operação de definição SAS falha, uma vez que não existe uma política de acesso ao utilizador correspondente ao utilizador2 no cofre de chaves.

**Resolução**: Para contornar este problema, crie uma política de acesso ao utilizador para o utilizador2 no teclado que concede ao utilizador 2 permissão para configurar a conta de armazenamento gerida e gerar definições SAS. O Utilizador2 pode fazê-lo a partir da Azure PowerShell utilizando os cmdlets abaixo:

$userPrincipalId = $(Get-AzureRmADUser -UserPrincipalName "user2_email_address"). ID

Set-AzureRmKeyVaultAccessPolicy -VaultName "keyvaultname" -ObjectId $userPrincipalId -PermissionsToStorage obter, lista, excluir, definir, atualizar, regenerar a chave, getsas, listsas, deletesas, sets, recuperar, backup, restaurar, purgar


## <a name="disposeartefactstimedout"></a>DisporArtefactsTimedOut

**ID de erro:** 181008

**Mensagem de erro:** VM: VMName. Erro: Evento de timeout encontrado 'DisposeArtefactsTimeout' no estado &'['Gateway.Service.StateMachine.SnapshotReplication.SnapshotReplicationEngine+WaitingForArtefactsDisposalPreCycle' ('WaitingForArtefactsDisposalPreCycle')].».

**Possíveis Causas:**

O componente que tenta replicar dados para o Azure está em baixo ou não responde. As causas possíveis incluem:

- O serviço de gateway em funcionamento no aparelho Azure Migrate está em baixo.
- O serviço gateway está a ter problemas de conectividade com o service bus/event hub/appliance Storage account.

**Identificação da causa exata para o OutseArtefactsTimedOut e a resolução correspondente:**

1. Assegure que a aplicação Azure Migrate está em execução.
2. Verifique se o serviço de gateway está em funcionamento no aparelho:
   1.  Faça login no aparelho Azure Migrate utilizando um ambiente de trabalho remoto e faça o seguinte.

   2.  Abra os serviços da Microsoft MMC snap-in (executar > services.msc) e verifique se o "Microsoft Azure Gateway Service" está em execução. Se o serviço estiver parado ou não estiver a funcionar, inicie o serviço. Em alternativa, pode abrir o pedido de comando ou PowerShell e fazer: "Net Start asrgwy"

3. Verifique se há problemas de conectividade entre o aparelho Azure Migrate e a conta de armazenamento do aparelho: 

    Executar o seguinte comando depois de descarregar a azcopia no aparelho Azure Migrate:
    
    _banco de azcopia https://[conta].blob.core.windows.net/[contentor]? SAS_
    
    **Passos para executar o teste de referência de desempenho:**
    
      1. [Descarregue](../storage/common/storage-use-azcopy-v10.md) azcopy
        
      2. Procure a conta de armazenamento do aparelho no Grupo de Recursos. A Conta de Armazenamento tem um nome que se assemelha a migrategwsa. \* \* \* \* \* \* \* \* \* \* Este é o valor do parâmetro [conta] no comando acima.
        
      3. Procure a sua conta de armazenamento no portal Azure. Certifique-se de que a subscrição que utiliza para pesquisar é a mesma subscrição (subscrição-alvo) na qual a conta de armazenamento é criada. Vá a Contentores na secção serviço blob. Clique em +Recipiente e crie um Recipiente. Deixe o Nível de Acesso Público para o valor selecionado predefinido.
        
      4. Aceda à assinatura de acesso partilhado em Definições. Selecione o recipiente em "Tipo de Recurso Permitido". Clique em Gerar SAS e cadeia de conexão. Copie o valor SAS.
        
      5. Execute o comando acima no Comando Prompt substituindo conta, contentor, SAS pelos valores obtidos nos passos 2, 3 e 4, respectivamente.
        
      Em alternativa, faça o [download](https://go.microsoft.com/fwlink/?linkid=2138967) do Azure Storage Explore para o aparelho e tente carregar 10 bolhas de ~64 MB nas contas de armazenamento. Se não houver problema, o upload deve ser bem sucedido.
        
    **Resolução:** Se este teste falhar,&#39;há um problema de networking. Envolva a sua equipa de networking local para verificar problemas de conectividade. Normalmente, pode haver algumas definições de firewall que estão a causar as falhas.
    
4.  Verifique se há problemas de conectividade entre o aparelho Azure Migrate e o Service Bus:

    Este teste verifica se o aparelho Azure Migrate pode comunicar com o backend do Serviço de Nuvem Azure Migrate. O aparelho comunica ao backend de serviço através das filas de mensagens Service Bus e Event Hub. Para validar a conectividade do aparelho para o Service Bus, [descarregue](https://go.microsoft.com/fwlink/?linkid=2139104) o Service Bus Explorer, tente ligar-se ao aparelho Service Bus e efetue a mensagem de envio/receção. Se não houver problema, isto deve ser bem sucedido.

    **Passos para executar o teste:**

    1. Copie a cadeia de ligação do Service Bus que foi criado no Projeto Migrate
    2. Abra o Service Bus Explorer
    3. Ir para arquivar e depois ligar
    4. Cole a cadeia de ligação e clique em Ligar
    5. Isto abrirá o espaço de nome de autocarro de serviço
    6. Selecione Snapshot Manager no tópico. Clique no Snapshot Manager, selecione "Receber Mensagens" > selecione "peek", e clique em OK
    7. Se a ligação for bem sucedida, verá "[x] mensagens recebidas" na saída da consola. Se a ligação não for bem sucedida, verá uma mensagem indicando que a ligação falhou
    
    **Resolução:** Se este teste falhar, há um problema de networking. Envolva a sua equipa de networking local para verificar problemas de conectividade. Normalmente, pode haver algumas definições de firewall que estão a causar as falhas.

5. Problemas de conectividade entre o aparelho Azure Migrate e o Cofre da Chave Azure:

    Este teste verifica se há problemas de conectividade entre o aparelho Azure Migrate e o Cofre da Chave Azure. O Cofre-Chave é utilizado para gerir o acesso à conta de armazenamento utilizado para replicação.
    
    **Passos para verificar a conectividade:**
    
    1. Retire o Key Vault URI da lista de recursos do Grupo de Recursos correspondente ao Projeto Azure Migrate.
    
    1. Abrir a PowerShell no aparelho Azure Migrate e executar o seguinte comando:
    
    _chave de ligação test-net URI -P 443_
    
    Este comando tentará uma ligação TCP e devolverá uma saída.
    
     - Na saída, verifique o campo "_TcpTestSucceed_". Se o valor for "_Verdadeiro_", não há problema de conectividade entre o Aparelho Azure Migrate e o Cofre da Chave Azure. Se o valor for "Falso", há um problema de conectividade.
    
    **Resolução:** Se este teste falhar, existe um problema de conectividade entre o aparelho Azure Migrate e o Cofre da Chave Azure. Envolva a sua equipa de networking local para verificar problemas de conectividade. Normalmente, pode haver algumas definições de firewall que estão a causar as falhas.
    
## <a name="diskuploadtimedout"></a>DiskUploadTimedOut

**ID de erro:** 1011

**Mensagem de erro:** O upload de dados para diskPath, DiskId da máquina virtual VMName; A VMId não completou dentro do tempo esperado.

Este erro normalmente indica que o aparelho Azure Migrate que executa a replicação não consegue ligar-se aos Serviços de Nuvem Azure, ou que a replicação está a progredir lentamente, fazendo com que o ciclo de replicação se esgote.

As causas possíveis incluem:

- O aparelho Azure Migrate está em baixo.
- O serviço de porta de entrada de replicação do aparelho não está a funcionar.
- O serviço de gateway de replicação está a ter problemas de conectividade com um dos seguintes componentes de serviço Azure que são usados para replicação: Service Bus/Event Hub/Azure cache Storage Account/Azure Key Vault.
- O serviço gateway está a ser acelerado ao nível do vCenter enquanto tenta ler o disco.

**Identificar a causa principal e resolver o problema:**

1. Assegure que a aplicação Azure Migrate está em execução.
2. Verifique se o serviço de gateway está em funcionamento no aparelho:
   1.  Faça login no aparelho Azure Migrate utilizando um ambiente de trabalho remoto e faça o seguinte.

   2.  Abra os serviços da Microsoft MMC snap-in (executar > services.msc) e verifique se o "Microsoft Azure Gateway Service" está em execução. Se o serviço estiver parado ou não estiver a funcionar, inicie o serviço. Em alternativa, pode abrir o pedido de comando ou PowerShell e fazer: "Net Start asrgwy".


3. **Verifique se há problemas de conectividade entre o aparelho Azure Migrate e a conta de armazenamento de cache:** 

    Executar o seguinte comando depois de descarregar a azcopia no aparelho Azure Migrate:
    
    _banco de azcopia https://[conta].blob.core.windows.net/[contentor]? SAS_
    
    **Passos para executar o teste de referência de desempenho:**
    
      1. [Descarregue](../storage/common/storage-use-azcopy-v10.md) azcopy
        
      2. Procure a conta de armazenamento do aparelho no Grupo de Recursos. A Conta de Armazenamento tem um nome que se assemelha a migratelsa. \* \* \* \* \* \* \* \* \* \* Este é o valor do parâmetro [conta] no comando acima.
        
      3. Procure a sua conta de armazenamento no portal Azure. Certifique-se de que a subscrição que utiliza para pesquisar é a mesma subscrição (subscrição-alvo) na qual a conta de armazenamento é criada. Vá a Contentores na secção serviço blob. Clique em +Recipiente e crie um Recipiente. Deixe o Nível de Acesso Público para o valor selecionado predefinido.
        
      4. Aceda à assinatura de acesso partilhado em Definições. Selecione o recipiente em "Tipo de Recurso Permitido". Clique em Gerar SAS e cadeia de conexão. Copie o valor SAS.
        
      5. Execute o comando acima no Comando Prompt substituindo conta, contentor, SAS pelos valores obtidos nos passos 2, 3 e 4, respectivamente.
        
      Em alternativa, faça o [download](https://go.microsoft.com/fwlink/?linkid=2138967) do Azure Storage Explore para o aparelho e tente carregar 10 bolhas de ~64 MB nas contas de armazenamento. Se não houver problema, o upload deve ser bem sucedido.
        
    **Resolução:** Se este teste falhar,&#39;há um problema de networking. Envolva a sua equipa de networking local para verificar problemas de conectividade. Normalmente, pode haver algumas definições de firewall que estão a causar as falhas.
                
4.  **Problemas de conectividade entre o aparelho Azure Migrate e o Azure Service Bus:**

    Este teste verificará se o aparelho Azure Migrate pode comunicar com o backend do Serviço de Nuvem Azure Migrate. O aparelho comunica ao backend de serviço através das filas de mensagens Service Bus e Event Hub. Para validar a conectividade do aparelho para o Service Bus, [descarregue](https://go.microsoft.com/fwlink/?linkid=2139104) o Service Bus Explorer, tente ligar-se ao aparelho Service Bus e efetue a mensagem de envio/receção. Se não houver problema, isto deve ser bem sucedido.

    **Passos para executar o teste:**
    
    1. Copie a cadeia de ligação do Service Bus que foi criado no Grupo de Recursos correspondente ao Projeto Azure Migrate
    
    1. Explorador de ônibus de serviço aberto
    
    1. Ir para o Arquivo > Ligar
    
    1. Cole a cadeia de ligação que copiou no passo 1 e clique em Ligar
    
    1. Isto abrirá o espaço de nomes do Service Bus.
    
    1. Selecione Snapshot Manager no tópico no espaço de nomes. Clique no Snapshot Manager, selecione "Receber Mensagens" > selecione "espreitar", e clique em OK.
    
    Se a ligação for bem sucedida, verá "[x] mensagens recebidas" na saída da consola. Se a ligação não for bem sucedida, verá uma mensagem indicando que a ligação falhou.
    
    **Resolução:** Se este teste falhar, existe um problema de conectividade entre o aparelho Azure Migrate e o Service Bus. Envolva a sua equipa de networking local para verificar estes problemas de conectividade. Normalmente, pode haver algumas definições de firewall que estão a causar as falhas.
    
 5. **Problemas de conectividade entre o aparelho Azure Migrate e o Cofre da Chave Azure:**

    Este teste verifica se há problemas de conectividade entre o aparelho Azure Migrate e o Cofre da Chave Azure. O Cofre-Chave é utilizado para gerir o acesso à conta de armazenamento utilizado para replicação.
    
    **Passos para verificar a conectividade:**
    
    1. Retire o Key Vault URI da lista de recursos do Grupo de Recursos correspondente ao Projeto Azure Migrate.
    
    1. Abrir a PowerShell no aparelho Azure Migrate e executar o seguinte comando:
    
    _chave de ligação test-net URI -P 443_
    
    Este comando tentará uma ligação TCP e devolverá uma saída.
    
    1. Na saída, verifique o campo "_TcpTestSucceed_". Se o valor for "_Verdadeiro_", não há problema de conectividade entre o Aparelho Azure Migrate e o Cofre da Chave Azure. Se o valor for "Falso", há um problema de conectividade.
    
    **Resolução:** Se este teste falhar, existe um problema de conectividade entre o aparelho Azure Migrate e o Cofre da Chave Azure. Envolva a sua equipa de networking local para verificar problemas de conectividade. Normalmente, pode haver algumas definições de firewall que estão a causar as falhas.
    
## <a name="encountered-an-error-while-trying-to-fetch-changed-blocks"></a>Encontrou um erro ao tentar buscar blocos alterados

Error Message: "Encontrei um erro ao tentar buscar blocos de mudança"

O método de replicação sem agente utiliza a tecnologia de rastreio de blocos alterada (CBT) da VMware para replicar dados para o Azure. O CBT permite que a ferramenta de migração do servidor rastreie e replica apenas os blocos que mudaram desde o último ciclo de replicação. Este erro ocorre se o controlo de blocos alterados de uma máquina virtual em replicação for reposto ou se o ficheiro de controlo de blocos alterados estiver danificado.

Este erro pode ser resolvido das duas seguintes formas:

- Se tivesse optado por "reparar automaticamente a replicação" selecionando "Sim" quando desencadeou a replicação de VM, a ferramenta tentará repará-la para si. Clique no VM e selecione "Reparação replicação".
- Se não optou por "reparar automaticamente a replicação" ou o passo acima não funcionou para si, então pare a replicação para a máquina virtual, [reinicie](https://go.microsoft.com/fwlink/?linkid=2139203) o rastreio do bloco alterado na máquina virtual e, em seguida, reconfigure a replicação.

Um desses problemas conhecidos que pode causar um reset CBT da máquina virtual em VMware vSphere 5.5 é descrito em [VMware KB 2048201: O Rastreio de Blocos Alterado](https://go.microsoft.com/fwlink/?linkid=2138888) é reiniciado após uma operação de armazenamento vMotion em vSphere 5.x . Se estiver a utilizar o VMware vSphere 5.5, confirme que aplica as atualizações descritas neste artigo da KB.

Em alternativa, pode redefinir o rastreio de bloco alterado VMware numa máquina virtual utilizando o VMware PowerCLI.

## <a name="an-internal-error-occurred"></a>Ocorreu um erro interno

Por vezes, pode atingir um erro que ocorre devido a problemas no ambiente/API VMware. Identificamos o seguinte conjunto de erros como erros relacionados com o ambiente VMware. Estes erros têm um formato fixo.

_Error Message: Ocorreu um erro interno. [Mensagem de erro]_

Por exemplo: Error Message: Ocorreu um erro interno. [Foi detetada uma configuração de instantâneo inválida].

A secção seguinte lista alguns dos erros de VMware geralmente vistos e como pode atenuá-los.

### <a name="error-message-an-internal-error-occurred-server-refused-connection"></a>Error Message: Ocorreu um erro interno. [Ligação recusada do servidor]

O problema é um problema conhecido da VMware e ocorre em VDDK 6.7. Tem de parar o serviço de gateway que funciona no aparelho Azure Migrate, [descarregar uma atualização a partir do VMware KB](https://go.microsoft.com/fwlink/?linkid=2138889)e reiniciar o serviço gateway.

Passos para parar o serviço de gateway:

1. Pressione Janelas + R, serviços abertos.msc. Clique em "Microsoft Azure Gateway Service", e pare-o.
2. Em alternativa, pode abrir o pedido de comando ou PowerShell e fazer: Net Stop asrgwy. Certifique-se de que espera até receber a mensagem de que o serviço já não está em funcionamento.

Passos para iniciar o serviço de gateway:

1. Pressione Janelas + R, serviços abertos.msc. Clique em "Microsoft Azure Gateway Service", e inicie-o.
2. Em alternativa, pode abrir o pedido de comando ou PowerShell e fazer: Net Start asrgwy.

### <a name="error-message-an-internal-error-occurred-an-invalid-snapshot-configuration-was-detected"></a>Error Message: Ocorreu um erro interno. ['Uma configuração de instantâneo inválida foi detetada.']

Se tiver uma máquina virtual com vários discos, poderá encontrar este erro se remover um disco da máquina virtual. Para remediar este problema, consulte os passos [deste artigo da VMware](https://go.microsoft.com/fwlink/?linkid=2138890).

### <a name="error-message-an-internal-error-occurred-generate-snapshot-hung"></a>Error Message: Ocorreu um erro interno. [Gere snapshot Hung]

Esta questão ocorre quando a geração instantânea deixa de responder. Quando este problema ocorre, pode ver a criação de paragens de tarefa instantânea a 95% ou 99%. Consulte este [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138969) para ultrapassar este problema.

### <a name="error-message-an-internal-error-occurred-failed-to-consolidate-the-disks-on-vm-_reasons_"></a>Error Message: Ocorreu um erro interno. [Falhou na consolidação dos discos em VM _[Razões]_]

Quando consolidamos os discos no fim do ciclo de replicação, a operação falha. Siga as instruções no [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138970) selecionando a _razão_ adequada para resolver o problema.

Os seguintes erros ocorrem quando as operações relacionadas com o instantâneo VMware – criar, eliminar ou consolidar discos falham. Siga as orientações na secção seguinte para remediar os erros:

### <a name="error-message-an-internal-error-occurred-another-task-is-already-in-progress"></a>Error Message: Ocorreu um erro interno. [Outra tarefa já está em curso]

Este problema ocorre quando existem tarefas de máquina virtual conflituosas em segundo plano, ou quando uma tarefa dentro do vCenter Server se esgota. Siga a resolução fornecida no seguinte [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138891).

### <a name="error-message-an-internal-error-occurred-operation-not-allowed-in-current-state"></a>Error Message: Ocorreu um erro interno. [Operação não permitida no estado atual]

Este problema ocorre quando os agentes de gestão do vCenter Server deixam de funcionar. Para resolver este problema, consulte a resolução no seguinte [VMware KB](https://go.microsoft.com/fwlink/?linkid=2138971).

### <a name="error-message-an-internal-error-occurred-snapshot-disk-size-invalid"></a>Error Message: Ocorreu um erro interno. [Tamanho do disco instantâneo inválido]

Esta é uma questão vMware conhecida em que o tamanho do disco indicado por instantâneo torna-se zero. Siga a resolução dada no [VMware KB](https://kb.vmware.com/s/).

### <a name="error-message-an-internal-error-occurred-memory-allocation-failed-out-of-memory"></a>Error Message: Ocorreu um erro interno. [A atribuição de memória falhou. Fora de memória.]

Isto acontece quando o amortecedor de anfitrião NFC está fora de memória. Para resolver este problema, é necessário mover o VM (compute vMotion) para um anfitrião diferente, que tem recursos gratuitos.

## <a name="replication-cycle-failed"></a>Ciclo de replicação falhou

**ID de erro:** 181008

**Mensagem de erro:** VM: 'VMName'. Erro: Não foram encontrados tiros de disco para a replicação instantânea com id snapshot : 'SnapshotID'.

**Possíveis Causas:**

As razões possíveis são:
1. Caminho de um ou mais discos incluídos alterados devido à VMotion de Armazenamento.
2. Um ou mais discos incluídos já não estão ligados ao VM.
      
**Recomendação:**

Seguem-se as recomendações
1. Restaurar os discos incluídos para o caminho original usando o armazenamento vMotion e, em seguida, desativar a vmoção de armazenamento.
2. Desativar a VMotion de Armazenamento, se ativada, pare a replicação na máquina virtual e replique novamente a máquina virtual. Se o problema persistir, contacte o suporte.

## <a name="next-steps"></a>Passos Seguintes

Continue a replicação em VM e realize [a migração de testes](./tutorial-migrate-vmware.md#run-a-test-migration).
