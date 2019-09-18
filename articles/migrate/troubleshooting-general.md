---
title: Solucionar problemas de migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos problemas conhecidos no serviço de migrações para Azure, bem como dicas de solução de problemas para erros comuns.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: raynew
ms.openlocfilehash: 49c43d393ef0722424088e0073942b56787f1bc7
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71067811"
---
# <a name="troubleshoot-azure-migrate"></a>Resolver problemas do Azure Migrate

As [migrações para Azure](migrate-services-overview.md) fornecem um hub de ferramentas para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros. Este artigo ajuda você a solucionar problemas com migrações para Azure, avaliação de servidor migrações para Azure e migração de servidor de migrações para Azure.


## <a name="find-a-project"></a>Localizar um projeto

Há [duas versões](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) de migrações para Azure.


Se você criou o projeto de migrações para Azure na versão atual das migrações para Azure, faça o seguinte:

1. Na [portal do Azure](https://portal.azure.com), pesquise **migrações para Azure**.
2. No painel migrações para Azure > **servidores**, selecione **alterar** no canto superior direito.

    ![Alternar para um projeto atual de migrações para Azure](./media/troubleshooting-general/switch-project.png)

3. Selecione a assinatura apropriada e o projeto de migrações para Azure.


Se você criou o projeto na versão anterior do migrações para Azure, faça o seguinte:

1. Na [portal do Azure](https://portal.azure.com), pesquise **migrações para Azure**.
2. No painel migrações para Azure, se você tiver criado um projeto na versão anterior, um banner referenciando projetos mais antigos será exibido. Selecione a faixa.

    ![Acessar projetos existentes](./media/troubleshooting-general/access-existing-projects.png)

3. Examine a lista de projetos antigos.


## <a name="create-additional-projects"></a>Criar projetos adicionais

Crie um novo projeto de migrações para Azure da seguinte maneira:

1. Na [portal do Azure](https://portal.azure.com), pesquise **migrações para Azure**.
2. No painel migrações para Azure > **servidores**, selecione **alterar** no canto superior direito.

   ![Alterar projeto de migrações para Azure](./media/troubleshooting-general/switch-project.png)

3. Para criar um novo projeto, selecione **clique aqui**.

   ![Criar um segundo projeto de migrações para Azure](./media/troubleshooting-general/create-new-project.png)

## <a name="review-supported-geographies"></a>Examinar regiões com suporte

Examine as geografias com suporte para [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

## <a name="delete-projectsworkspaces"></a>Excluir projetos/espaços de trabalho

Ao excluir projetos de migração do Azure e espaços de trabalho Log Analytics, observe que:

- Quando você exclui um projeto de migrações para Azure, o projeto *e* os metadados sobre computadores descobertos são excluídos.
- Se você anexou um espaço de trabalho Log Analytics à ferramenta de avaliação do servidor, o espaço de trabalho não será excluído automaticamente.
- O mesmo espaço de trabalho Log Analytics pode ser usado para vários cenários.
- Se você quiser excluir o espaço de trabalho Log Analytics, deverá fazer isso manualmente.


### <a name="delete-a-project"></a>Excluir um projeto

Para excluir um projeto na versão atual do migrações para Azure:

1. Abra o grupo de recursos do Azure no qual o projeto foi criado.
2. Na página grupo de recursos, selecione **Mostrar tipos ocultos**.
3. Selecione o projeto de migração que você deseja excluir. O tipo de recurso é Microsoft. Migrate/migrateprojects e o exclui.

Para excluir um projeto na versão mais antiga do migrações para Azure:

1. Abra o grupo de recursos do Azure no qual o projeto foi criado.
2. Selecione o projeto de migração que você deseja excluir. O tipo de recurso é projeto de migração e o exclui.


### <a name="delete-a-workspace"></a>Eliminar uma área de trabalho

Navegue até o espaço de trabalho Log Analytics anexado ao projeto.
* Se você não tiver excluído o projeto de migrações para Azure, poderá encontrar o link para o espaço de trabalho na avaliação do **Essentials** > **Server**.
       ![Espaço de trabalho da LA](./media/troubleshooting-general/loganalytics-workspace.png)

     * If you've already deleted the Azure Migrate project, select **Resource Groups** in the left pane of the Azure portal. Locate the workspace in the relevant resources group, and [follow the instructions](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) to delete it.


## <a name="error-requests-must-contain-user-identity-headers"></a>Erro "as solicitações devem conter cabeçalhos de identidade do usuário"

Ao criar um projeto, esse erro pode indicar que você não tem acesso ao locatário do Azure Active Directory (Azure AD) da organização.

- Quando você é adicionado a um locatário do Azure AD pela primeira vez, você recebe um convite por email para ingressar no locatário.
- Aceite o convite para ser adicionado com êxito ao locatário.
    - Se você não conseguir ver o email, entre em contato com um usuário com acesso ao locatário e peça para [reenviar o convite](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) para você.
    - Depois de receber o email de convite, abra-o e selecione o link para aceitar o convite. Em seguida, saia do portal do Azure e entre novamente. (a atualização do navegador não funcionará.) Em seguida, você pode começar a criar o projeto de migração.


## <a name="error-invalid-ovf-manifest-entry"></a>Erro "entrada de manifesto OVF inválida"

Se você receber o erro "o arquivo de manifesto fornecido é inválido: Entrada de manifesto OVF inválida ", faça o seguinte:

1. Verifique se o arquivo OVA do dispositivo de migração do Azure foi baixado corretamente verificando seu valor de hash. [Saiba mais](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Se o valor de hash não corresponder, baixe o arquivo OVA novamente e repita a implantação.
2. Se a implantação ainda falhar e você estiver usando o VMware vSphere cliente para implantar o arquivo OVF, tente implantá-lo por meio do cliente Web vSphere. Se a implantação ainda falhar, tente usar um navegador da Web diferente.
3. Se você estiver usando o cliente Web vSphere e tentando implantá-lo no vCenter Server 6,5 ou 6,7, tente implantar o OVA diretamente no host ESXi:
   - Conecte-se diretamente ao host ESXi (em vez de vCenter Server) com o cliente Web (*endereço IP do host*do https://< >/UI).
   - Em**inventário** **doméstico** > , selecione **arquivo** > **implantar modelo de OVF**. Navegue até o OVA e conclua a implantação.
4. Se a implantação ainda falhar, entre em contato com o suporte para migrações para Azure.

## <a name="appliance-cant-connect-to-the-internet"></a>O dispositivo não pode se conectar à Internet

Isso pode acontecer se a máquina do dispositivo estiver atrás de um proxy.

- Certifique-se de fornecer as credenciais de autorização se o proxy precisar delas.
- Se você estiver usando um proxy de firewall baseado em URL para controlar a conectividade de saída, adicione essas URLs a uma lista de permissões:

    - [URLs para avaliação do VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [URLs para avaliação do Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [URLs para migração sem agente do VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [URLS para migração baseada em agente do VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [URLs para migração do Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- Se você estiver usando um proxy de interceptação para se conectar à Internet, importe o certificado de proxy para a VM do dispositivo usando [estas etapas](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="errordatetime-synchronization"></a>Erro: sincronização de data/hora

Um erro sobre a sincronização de data e hora (802) indica que o relógio do servidor pode estar fora de sincronização com a hora atual em mais de cinco minutos. Altere a hora do relógio na VM do coletor para corresponder à hora atual:

1. Abra um prompt de comando de administrador na VM.
2. Para verificar o fuso horário, execute **w32tm/TZ**.
3. Para sincronizar o tempo, execute **w32tm/resync**.


## <a name="error-unabletoconnecttoserver"></a>Erro: UnableToConnectToServer

Se você receber esse erro de conexão, talvez não consiga se conectar ao vCenter Server *ServerName*. com: 9443. Os detalhes do erro indicam que não há nenhum ponto de extremidade ouvindo em https://*ServerName*. com: 9443/SDK que pode aceitar a mensagem.

- Verifique se você está executando a versão mais recente do dispositivo. Se você não tiver, atualize o dispositivo para a [versão mais recente](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Se o problema ainda ocorrer na versão mais recente, o dispositivo poderá não conseguir resolver o nome de vCenter Server especificado ou a porta especificada poderá estar errada. Por padrão, se a porta não for especificada, o coletor tentará se conectar ao número da porta 443.

    1. Execute o ping *ServerName*. com do dispositivo.
    2. Se a etapa 1 falhar, tente se conectar ao servidor do vCenter usando o endereço IP.
    3. Identifique o número da porta correto para se conectar ao vCenter Server.
    4. Verifique se vCenter Server está em execução.


## <a name="error-appliance-might-not-be-registered"></a>Erro: O dispositivo pode não estar registrado

- O erro 60052, "o dispositivo pode não estar registrado com êxito no projeto de migrações para Azure" ocorrerá se a conta do Azure usada para registrar o dispositivo não tiver permissões suficientes.
    - Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos permissões de colaborador na assinatura.
    - [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as funções e permissões necessárias do Azure.
- O erro 60039, "o dispositivo pode não estar registrado com êxito no projeto de migrações para Azure" pode ocorrer se o registro falhar porque o projeto de migrações para Azure usado para registrar o dispositivo não foi encontrado.
    - No portal do Azure e verifique se o projeto existe no grupo de recursos.
    - Se o projeto não existir, crie um novo projeto de migrações para Azure em seu grupo de recursos e registre o dispositivo novamente. [Saiba como](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) criar um novo projeto.

## <a name="error-key-vault-management-operation-failed"></a>Erro: Falha na operação de gerenciamento de Key Vault

Se você receber o erro 60030 ou 60031, "falha em uma operação de gerenciamento de Azure Key Vault", faça o seguinte:
- Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos permissões de colaborador na assinatura.
- Verifique se a conta tem acesso ao cofre de chaves especificado na mensagem de erro e repita a operação.
- Se o problema persistir, entre em contato com o suporte da Microsoft.
- [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as funções e permissões do Azure necessárias.

## <a name="fix-discovery-couldnt-be-initiated"></a>Soluciona Não foi possível iniciar a descoberta

Erro 60028: "Não foi possível iniciar a descoberta devido a um erro. A operação falhou para a lista especificada de hosts ou clusters "indica que a descoberta não pôde ser iniciada nos hosts listados no erro devido a um problema no acesso ou na recuperação de informações da VM. O restante dos hosts foi adicionado com êxito.

- Adicione os hosts listados no erro novamente, usando a opção **Adicionar host** .
- Se houver um erro de validação, examine as diretrizes de correção para corrigir os erros e tente a opção **salvar e iniciar descoberta** novamente.

## <a name="fix-azure-ad-operation-failed-60025"></a>Soluciona Falha na operação do Azure AD (60025)

Erro 60025: "Falha em uma operação do Azure AD. O erro ocorreu ao criar ou atualizar o aplicativo do Azure AD "ocorre quando a conta de usuário do Azure usada para iniciar a descoberta é diferente da conta usada para registrar o dispositivo. Efetue uma das seguintes ações:

- Verifique se a conta de usuário que está iniciando a descoberta é a mesma usada para registrar o dispositivo.
- Forneça Azure Active Directory permissões de acesso do aplicativo à conta de usuário para a qual a operação de descoberta está falhando.
- Exclua o grupo de recursos criado anteriormente para o projeto de migrações para Azure. Crie outro grupo de recursos para iniciar novamente.
- [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as permissões de aplicativo Azure Active Directory.


## <a name="discovered-vms-not-in-portal"></a>VMs descobertas não estão no portal

Se você iniciar a descoberta para que a **avaliação do servidor** e a **migração do servidor** mostrem a **descoberta em andamento**, mas ainda não veja as VMs no portal, observe o seguinte:

- Depois de iniciar a descoberta do dispositivo, leva cerca de 15 minutos para uma descoberta de VM VMware e cerca de dois minutos para cada host adicionado para a descoberta de VM do Hyper-V.
- Se você continuar vendo a **descoberta em andamento** mesmo após esses períodos de espera, selecione **Atualizar** na guia **servidores** . Isso deve mostrar a contagem dos servidores descobertos na **avaliação do servidor** e na migração do **servidor**.


## <a name="deleted-vms-in-the-portal"></a>VMs excluídas no portal

Se você tiver implantado um dispositivo que descobre continuamente seu ambiente local, mas as VMs excluídas ainda estão sendo exibidas no portal, observe o seguinte:  

- Leva até 30 minutos para que os dados de descoberta coletados pelo dispositivo sejam refletidos no Portal.
- Se você não vir informações atualizadas após 30 minutos, atualize os dados seguindo estas etapas:

    1. Em **servidores** > **migração do Azure migrar servidor**, selecione **visão geral**.
    2. Em **gerenciar**, selecione **integridade do agente**
    3. Selecione **Atualizar agente**.
    1. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas.

## <a name="vm-information-isnt-in-the-portal"></a>As informações da VM não estão no portal

- Leva até 30 minutos para que os dados de descoberta coletados pelo dispositivo sejam refletidos no Portal.
- Se você não vir informações atualizadas após 30 minutos, atualize os dados seguindo estas etapas:

    1. Em **servidores** > **migração do Azure migrar servidor**, selecione **visão geral**.
    2. Em **gerenciar**, selecione **integridade do agente**
    3. Selecione **Atualizar agente**.
    1. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas.


## <a name="fix-cant-connect-to-host-or-cluster"></a>Soluciona Não é possível conectar-se ao host ou cluster

Erro 50004: "Não é possível se conectar a um host ou cluster porque o nome do servidor não pode ser resolvido. Código de erro WinRM: 0x803381B9 "poderá ocorrer se o serviço DNS do Azure para o dispositivo não puder resolver o cluster ou o nome do host fornecido.

- Se você vir esse erro no cluster, o FQDN do cluster.
- Você também pode ver esse erro para hosts em um cluster. Isso indica que o dispositivo pode se conectar ao cluster, mas o cluster retorna nomes de host que não são FQDNs. Para resolver esse erro, atualize o arquivo de hosts no dispositivo adicionando um mapeamento do endereço IP e nomes de host:
    1. Abra o bloco de notas como administrador.
    2. Abra o arquivo C:\Windows\System32\Drivers\etc\hosts.
    3. Adicione o endereço IP e o nome do host em uma linha. Repita para cada host ou cluster em que você vê esse erro.
    4. Guarde e feche o ficheiro de anfitriões.
    5. Verifique se o dispositivo pode se conectar aos hosts, usando o aplicativo de gerenciamento de dispositivo. Após 30 minutos, você deverá ver as informações mais recentes para esses hosts na portal do Azure.



## <a name="fix-assessment-readiness"></a>Corrigir preparação de avaliação

Corrija os problemas de preparação da avaliação da seguinte maneira:

**Lo** | **Soluciona**
--- | ---
Tipo de arranque não suportado | O Azure não dá suporte a VMs com um tipo de inicialização EFI. Recomendamos que você converta o tipo de inicialização para BIOS antes de executar uma migração. <br/><br/>Você pode usar a migração de servidor de migrações para o Azure para lidar com a migração dessas VMs. Ele converterá o tipo de inicialização da VM para BIOS durante a migração.
Sistema operacional Windows com suporte condicional | O sistema operacional passou pela data de fim do suporte e precisa de um contrato de suporte personalizado (CSA) para [dar suporte no Azure](https://aka.ms/WSosstatement). Considere a atualização antes de migrar para o Azure.
Sistema operacional Windows sem suporte | O Azure dá suporte apenas a [versões selecionadas do sistema operacional Windows](https://aka.ms/WSosstatement). Considere atualizar o computador antes de migrar para o Azure.
SO Linux condicionalmente endossado | O Azure endossa apenas [as versões selecionadas do sistema operacional Linux](../virtual-machines/linux/endorsed-distros.md). Considere atualizar o computador antes de migrar para o Azure.
SO Linux não endossado | O computador pode iniciar no Azure, mas o Azure não fornece suporte ao sistema operacional. Considere atualizar para uma [versão do Linux endossada](../virtual-machines/linux/endorsed-distros.md) antes de migrar para o Azure.
Sistema operacional desconhecido | O sistema operacional da VM foi especificado como "other" em vCenter Server. Esse comportamento impede que as migrações para Azure verifiquem a prontidão do Azure da VM. Verifique se o sistema operacional tem [suporte](https://aka.ms/azureoslist) do Azure antes de migrar o computador.
Versão de bits sem suporte | As VMs com sistemas operacionais de 32 bits podem ser inicializadas no Azure, mas recomendamos que você atualize para 64 bits antes de migrar para o Azure.
Requer uma assinatura Microsoft Visual Studio | O computador está executando um sistema operacional cliente Windows, que tem suporte apenas por meio de uma assinatura do Visual Studio.
VM não encontrada para o desempenho de armazenamento necessário | O desempenho de armazenamento (operações de entrada/saída por segundo [IOPS] e taxa de transferência) necessários para o computador excede o suporte à VM do Azure. Reduza os requisitos de armazenamento para a máquina antes da migração.
VM não encontrada para o desempenho de rede necessário | O desempenho de rede (entrada/saída) necessário para o computador excede o suporte de VM do Azure. Reduza os requisitos de rede para o computador.
VM não encontrada no local especificado | Use um local de destino diferente antes da migração.
Um ou mais discos não são adequados | Um ou mais discos anexados à VM não atendem aos requisitos do Azure. Um<br/><br/> Azure Migrate: Atualmente, a avaliação do servidor não dá suporte a discos SSD Ultra e avalia os discos com base nos limites de disco para discos gerenciados Premium (32 TB).<br/><br/> Para cada disco anexado à VM, verifique se o tamanho do disco é < 64 TB (com suporte de discos SSD Ultra).<br/><br/> Se não estiver, reduza o tamanho do disco antes de migrar para o Azure, ou use vários discos no Azure e distribua [-os juntos](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) para obter limites de armazenamento maiores. Certifique-se de que o desempenho (IOPS e taxa de transferência) necessário para cada disco tem suporte dos [discos de máquina virtual gerenciada](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)pelo Azure.
Um ou mais adaptadores de rede inadequados. | Remova adaptadores de rede não utilizados do computador antes da migração.
A contagem do disco excede o limite | Remova os discos não utilizados do computador antes da migração.
O tamanho do disco excede o limite | Azure Migrate: Atualmente, a avaliação do servidor não dá suporte a discos SSD Ultra e avalia os discos com base nos limites de disco Premium (32 TB).<br/><br/> No entanto, o Azure dá suporte a discos com tamanho de até 64 TB (com suporte de discos SSD Ultra). Reduza os discos para menos de 64 TB antes da migração ou use vários discos no Azure e distribua [-os juntos](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) para obter limites de armazenamento mais altos.
Disco não disponível no local especificado | Verifique se o disco está em seu local de destino antes de migrar.
Disco não disponível para a redundância especificada | O disco deve usar o tipo de armazenamento de redundância definido nas configurações de avaliação (LRS por padrão).
Não foi possível determinar a adequação do disco devido a um erro interno | Tente criar uma nova avaliação para o grupo.
VM com núcleos necessários e memória não encontrada | O Azure não pôde encontrar um tipo de VM adequado. Reduza a memória e o número de núcleos do computador local antes de migrar.
Não foi possível determinar a adequação da VM devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação para um ou mais discos devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação para um ou mais adaptadores de rede devido a um erro interno | Tente criar uma nova avaliação para o grupo.

## <a name="linux-vms-are-conditionally-ready"></a>As VMs do Linux estão "prontamente prontas"

A avaliação do servidor marca as VMs do Linux como "prontamente prontas" devido a uma lacuna conhecida na avaliação do servidor.

- A lacuna impede que ele detecte a versão secundária do sistema operacional Linux instalada nas VMs locais.
- Por exemplo, para RHEL 6,10, atualmente a avaliação do servidor detecta apenas RHEL 6 como a versão do sistema operacional.
-  Como o Azure endossa apenas versões específicas do Linux, as VMs do Linux estão atualmente marcadas como prontas para avaliação de servidor.
- Você pode determinar se o SO Linux em execução na VM local é endossado no Azure examinando o [suporte do Azure Linux](https://aka.ms/migrate/selfhost/azureendorseddistros).
-  Depois de verificar a distribuição endossada, você pode ignorar este aviso.

## <a name="azure-skus-exceed-on-premises-sizing"></a>Os SKUs do Azure excedem o dimensionamento local

A avaliação de servidor de migrações para Azure pode recomendar SKUs de VM do Azure com mais núcleos e memória do que a alocação local atual com base no tipo de avaliação:


- A recomendação de SKU de VM depende das propriedades de avaliação.
- Isso é afetado pelo tipo de avaliação que você executa na avaliação do servidor: *Baseado em desempenho*ou *como local*.
- Para avaliações baseadas em desempenho, a avaliação do servidor considera os dados de utilização das VMs locais (CPU, memória, disco e utilização de rede) para determinar o SKU de VM de destino correto para suas VMs locais. Ele também adiciona um fator de conforto ao determinar a utilização efetiva.
- Para o dimensionamento local, os dados de desempenho não são considerados e o SKU de destino é recomendado com base na alocação local.

Para mostrar como isso pode afetar as recomendações, vamos usar um exemplo:

Temos uma VM local com quatro núcleos e oito GB de memória, com 50% de utilização da CPU e 50% de utilização da memória e um fator de conforto especificado de 1,3.

-  Se a avaliação for **como local**, é recomendável um SKU de VM do Azure com 4 núcleos e 8 GB de memória.
- Se a avaliação for baseada em desempenho, com base na utilização efetiva de CPU e memória (50% de 4 núcleos * 1,3 = 2,6 núcleos e 50% de 8 GB de memória * 1,3 = 5,3 GB de memória), o SKU de VM mais barato de quatro núcleos (contagem de núcleos com suporte mais próximo) e oito GB de memória (com suporte mais próximo tamanho da memória) é recomendado.
- [Saiba mais](concepts-assessment-calculation.md#sizing) sobre o dimensionamento da avaliação.

## <a name="azure-disk-skus-bigger-than-on-premises"></a>SKUs de disco do Azure maiores do que localmente

A avaliação de servidor de migrações para Azure pode recomendar um disco maior com base no tipo de avaliação.
- O dimensionamento de disco na avaliação do servidor depende de duas propriedades de avaliação: critérios de dimensionamento e tipo de armazenamento.
- Se os critérios de dimensionamento forem **baseados em desempenho**e o tipo de armazenamento for definido como **automático**, os valores de IOPS e taxa de transferência do disco serão considerados ao identificar o tipo de disco de destino (HDD Standard, SSD Standard ou Premium). Um SKU de disco do tipo de disco é recomendado, e a recomendação considera os requisitos de tamanho do disco local.
- Se os critérios de dimensionamento forem **baseados em desempenho**e o tipo de armazenamento for **Premium**, um SKU de disco Premium no Azure será recomendado com base nos requisitos de IOPS, taxa de transferência e tamanho do disco local. A mesma lógica é usada para executar o dimensionamento de disco quando os critérios de dimensionamento são **locais** e o tipo de armazenamento é **HDD Standard**, **SSD Standard**ou **Premium**.

Por exemplo, se você tiver um disco local com 32 GB de memória, mas o IOPS agregado de leitura e gravação para o disco for de 800 IOPS, a avaliação do servidor recomendará um disco Premium (devido aos requisitos de IOPS mais altos) e, em seguida, recomendará uma SKU de disco que possa dar suporte ao r IOPS e tamanho de ecessário. A correspondência mais próxima neste exemplo seria P15 (256 GB, IOPS de 1100). Embora o tamanho exigido pelo disco local tenha 32 GB, a avaliação do servidor recomenda um disco maior devido ao requisito de IOPS alto do disco local.

## <a name="fix-percentage-of-utilized-core-or-memory-missing"></a>Soluciona Porcentagem de núcleo utilizado ou memória ausente

A avaliação do servidor relata "PercentageOfCoresUtilizedMissing" ou "PercentageOfMemoryUtilizedMissing" quando o dispositivo de migrações para Azure não pode coletar dados de desempenho para as VMs locais relevantes.

- Isso pode ocorrer se as VMs estiverem desativadas durante a duração da avaliação. O dispositivo não pode coletar dados de desempenho para uma VM quando ela está desativada.
- Se apenas os contadores de memória estiverem ausentes e você estiver tentando avaliar as VMs do Hyper-V, verifique se você tem memória dinâmica habilitada nessas VMs. Há um problema conhecido apenas para VMs do Hyper-V, em que um dispositivo de migrações para Azure não pode coletar dados de utilização de memória para VMs que não têm memória dinâmica habilitada.
- Se algum dos contadores de desempenho estiver ausente, a avaliação do servidor de migrações para Azure voltará para os núcleos e a memória alocados e recomendará um tamanho de VM correspondente.

## <a name="is-the-vm-os-license-cost-included-in-cost-assessment"></a>O custo da licença do sistema operacional da VM está incluído na avaliação de custo?

A avaliação de servidor de migrações para Azure atualmente considera o custo de licença do sistema operacional somente para computadores Windows. Os custos de licença para computadores Linux não são atualmente considerados.

## <a name="performance-history-and-percentile-use"></a>Histórico de desempenho e uso de percentil

Essas propriedades se aplicam somente ao dimensionamento baseado em desempenho na avaliação do servidor de migrações para Azure.

A Avaliação do Servidor recolhe continuamente os dados de desempenho das máquinas no local e utiliza-os para recomendar o SKU da VM e o SKU do disco no Azure. Esses dados de desempenho são coletados pela avaliação do servidor da seguinte maneira:
- O dispositivo de migrações para Azure cria perfis continuamente no ambiente local para coletar dados de utilização em tempo real a cada 20 segundos para VMs VMware e a cada 30 segundos para VMs do Hyper-V.
- O dispositivo acumula os exemplos de 20 ou 30 segundos para criar um único ponto de dados para cada 10 minutos. Para criar o ponto de dados único, o dispositivo seleciona o valor de pico de todas as amostras de 20 segundos e 30 segundos e, em seguida, envia-o para o Azure.
- Quando cria uma avaliação na Avaliação do Servidor, com base na duração do desempenho e no valor de percentil do histórico de desempenho, o valor de utilização representativo é identificado. Por exemplo, se o histórico de desempenho for uma semana e a utilização de percentil for 95 º, as migrações para Azure classificarão todos os pontos de exemplo de 10 minutos para a última semana em ordem crescente e, em seguida, selecionará o 95 º percentil como o valor representativo.
- O valor do 95 º percentil garante que você ignore quaisquer exceções, que poderão ser incluídas se você escolher o 99 º percentil.
- Se você quiser escolher o pico de uso do período e não quiser perder as exceções, deverá selecionar o 99 º percentil para utilização de percentil.



## <a name="i-cant-find-dependency-visualization-for-azure-government"></a>Não consigo encontrar a visualização de dependência para o Azure governamental

As migrações para Azure dependem Mapa do Serviço para a funcionalidade de visualização de dependência. Como Mapa do Serviço não está disponível no Azure governamental no momento, essa funcionalidade não está disponível no Azure governamental.

## <a name="dependencies-dont-show-after-installing-agents"></a>As dependências não são mostradas após a instalação de agentes


Depois de instalar os agentes de visualização de dependência em VMs locais, as migrações para Azure normalmente leva 15-30 minutos para exibir as dependências no Portal. Se você tiver aguardado por mais de 30 minutos, verifique se o Microsoft Monitoring Agent (MMA) pode se conectar ao espaço de trabalho Log Analytics.

Para VMs do Windows:
1. No painel de controle, inicie o MMA.
2. Nas **Propriedades** > de Microsoft Monitoring Agent**log Analytics do Azure (OMS)** , verifique se o **status** do espaço de trabalho está verde.
3. Se o status não for verde, tente remover o espaço de trabalho e adicioná-lo novamente ao MMA.

      ![Caixa de diálogo Propriedades de MMA](./media/troubleshooting-general/mma-status.png)

Para VMs do Linux, certifique-se de que os comandos de instalação para MMA e o Dependency Agent tenham êxito.

## <a name="supported-mma-os"></a>SO MMA com suporte

 Examine os sistemas operacionais [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) com suporte.

## <a name="supported-dependency-agent-os"></a>Sistema operacional do agente de dependência com suporte

Examine os sistemas operacionais [Windows e Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) com suporte.

## <a name="dependencies-for-more-than-an-hour"></a>Dependências por mais de uma hora

Embora as migrações para Azure permitam que você volte para uma data específica no último mês, a duração máxima para a qual você pode visualizar as dependências é de uma hora.

Por exemplo, você pode usar a funcionalidade duração de tempo no mapa de dependências para exibir dependências de ontem, mas você pode exibi-las somente para um período de uma hora.

No entanto, você pode usar os logs de Azure Monitor para [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração maior.

## <a name="i-cant-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Não consigo visualizar dependências de grupos com mais de 10 VMs

Na avaliação do servidor de migrações para Azure, você pode [Visualizar dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) com até 10 VMS. Para grupos maiores, recomendamos que você divida as VMs em grupos menores para visualizar as dependências.

## <a name="machines-show-install-agent-not-view-dependencies"></a>As máquinas mostram "instalar o agente" não "Exibir dependências"

Após a migração de computadores com a visualização de dependência habilitada para o Azure, as máquinas podem mostrar a ação "instalar agente" em vez de "Exibir dependências" devido ao seguinte comportamento:


- Após a migração para o Azure, as máquinas locais são desativadas e as VMs equivalentes são giradas no Azure. Esses computadores adquirem um endereço MAC diferente.
- Os computadores também podem ter um endereço IP diferente, dependendo se você manteve o endereço IP local ou não.
- Se os endereços MAC e IP forem diferentes do local, as migrações para Azure não associarão os computadores locais a qualquer Mapa do Serviço dados de dependência. Nesse caso, ele mostrará a opção para instalar o agente em vez de exibir as dependências.
- Após uma migração de teste para o Azure, as máquinas locais permanecem ativadas conforme o esperado. As máquinas equivalentes giradas no Azure adquirem um endereço MAC diferente e podem adquirir endereços IP diferentes. A menos que você bloqueie o tráfego de log Azure Monitor de saída desses computadores, as migrações para Azure não associarão os computadores locais a qualquer Mapa do Serviço dados de dependência e, portanto, mostrarão a opção de instalar agentes, em vez de exibir dependências.


## <a name="collect-network-traffic-logs-in-portal"></a>Coletar logs de tráfego de rede no portal

Colete logs da seguinte maneira:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Pressione F12 para iniciar a Ferramentas para Desenvolvedores. Se necessário, desmarque a configuração **Limpar entradas na navegação** .
3. Selecione a guia **rede** e inicie a captura do tráfego de rede:
   - No Chrome, selecione **preservar log**. A gravação deve ser iniciada automaticamente. Um círculo vermelho indica que o tráfego está sendo capturado. Se o círculo vermelho não aparecer, selecione o círculo preto para iniciar.
   - No Microsoft Edge e no Internet Explorer, a gravação deve ser iniciada automaticamente. Se não estiver, selecione o botão verde Play.
4. Tente reproduzir o erro.
5. Depois de encontrar o erro durante a gravação, interrompa a gravação e salve uma cópia da atividade gravada:
   - No Chrome, clique com o botão direito do mouse e selecione **salvar como Har com conteúdo**. Essa ação compacta e exporta os logs como um arquivo. Har.
   - No Microsoft Edge ou Internet Explorer, selecione a opção **Exportar tráfego capturado** . Essa ação compacta e exporta o log.
6. Selecione a guia **console** para verificar se há avisos ou erros. Para salvar o log do console:
   - No Chrome, clique com o botão direito do mouse em qualquer lugar no log do console. Selecione **salvar como**, para exportar e compactar o log.
   - No Microsoft Edge ou no Internet Explorer, clique com o botão direito do mouse nos erros e selecione **copiar tudo**.
7. Feche Ferramentas para Desenvolvedores.
