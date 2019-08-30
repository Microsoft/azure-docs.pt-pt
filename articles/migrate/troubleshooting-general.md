---
title: Solucionar problemas de migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos problemas conhecidos no serviço de migrações para Azure, bem como dicas de solução de problemas para erros comuns.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: raynew
ms.openlocfilehash: 7fea6d16c8846909a8ce9bb33aae74ce343018fa
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142328"
---
# <a name="troubleshoot-azure-migrate"></a>Resolver problemas do Azure Migrate

As migrações para [Azure](migrate-services-overview.md) fornecem um hub de ferramentas para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros. Este artigo ajuda a solucionar erros de migração do Azure, avaliação de servidor migrações para Azure e migração de servidor de migrações para Azure.

## <a name="azure-migrate-project-issues"></a>Problemas do projeto de migrações para Azure

### <a name="i-cant-find-my-existing-azure-migrate-project"></a>Não consigo encontrar meu projeto existente de migrações para Azure.

Há [duas versões](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) de migrações para Azure. Dependendo da versão na qual você criou o projeto, use um dos seguintes métodos:

* Se você estiver procurando um projeto criado com a versão anterior (antiga experiência) das migrações para Azure, siga estas etapas para encontrar o projeto:

    1. Vá para a [portal do Azure](https://portal.azure.com)e procure migrações para **Azure**.
    2. No painel migrações para Azure, você verá uma faixa que menciona o acesso a projetos mais antigos. Você verá essa faixa somente se tiver criado um projeto com a experiência antiga. Selecione a faixa.

       ![Acessar projetos existentes](./media/troubleshooting-general/access-existing-projects.png)

    3. Agora você verá a lista de projetos existentes que foram criados com a versão anterior do migrações para Azure.

* Se você estiver procurando um projeto criado com a versão atual (nova experiência), siga estas etapas para encontrar o projeto:

    1. Vá para a [portal do Azure](https://portal.azure.com)e procure migrações para **Azure**.
    2. No painel migrações para Azure, vá para a página **servidores** no painel esquerdo e selecione **alterar** no canto superior direito.

       ![Alternar para um projeto atual de migrações para Azure](./media/troubleshooting-general/switch-project.png)

    3. Selecione a assinatura apropriada e o projeto de migrações para Azure.

### <a name="how-do-i-create-a-second-azure-migrate-project"></a>Como fazer criar um segundo projeto de migrações para Azure?

Siga estas etapas para criar um novo projeto de migrações para Azure:

1. Vá para a [portal do Azure](https://portal.azure.com)e procure migrações para **Azure**.
2. No painel migrações para Azure, vá para a página **servidores** no painel esquerdo e selecione **alterar** no canto superior direito.

   ![Alterar projeto de migrações para Azure](./media/troubleshooting-general/switch-project.png)

3. Para criar um novo projeto, selecione **clique aqui**.

   ![Criar um segundo projeto de migrações para Azure](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-does-azure-migrate-support"></a>Quais Geografia do Azure há suporte para migrações para Azure?

Consulte as listas para [VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e para [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="how-do-i-delete-azure-migrate-projects-and-associated-log-analytics-workspaces"></a>Como fazer excluir projetos de migrações para Azure e espaços de trabalho de Log Analytics associados?

Quando você exclui um projeto de migrações para Azure, o projeto de migração *e* os metadados sobre computadores descobertos são excluídos. No entanto, se você anexou um espaço de trabalho Log Analytics à ferramenta de avaliação do servidor, o espaço de trabalho Log Analytics não será excluído automaticamente. (O mesmo espaço de trabalho Log Analytics pode ser usado para vários casos de uso.) Se você também quiser excluir o espaço de trabalho Log Analytics, deverá fazer isso manualmente:

1. Navegue até o espaço de trabalho Log Analytics anexado ao projeto.
     * Se você ainda não tiver excluído o projeto de migração, poderá encontrar o link para o espaço de trabalho na página Visão geral da avaliação do servidor na seção Essentials.

       ![Espaço de trabalho da LA](./media/troubleshooting-general/loganalytics-workspace.png)

     * Se você já tiver excluído o projeto de migração, selecione **grupos de recursos** no painel esquerdo da portal do Azure. Vá para o grupo de recursos no qual o espaço de trabalho foi criado e navegue até ele.
2. Siga as instruções em [excluir um espaço de trabalho log Analytics do Azure com o portal do Azure](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace).

### <a name="migration-project-creation-failed-with-a-requests-must-contain-user-identity-headers-error"></a>Falha na criação do projeto de migração com um erro "solicitações devem conter cabeçalhos de identidade do usuário".

Esse problema pode ocorrer para usuários que não têm acesso ao locatário do Azure Active Directory (Azure AD) da organização. Quando um usuário é adicionado a um locatário do Azure AD pela primeira vez, o usuário recebe um convite por email para ingressar no locatário. Os usuários devem aceitar o convite para serem adicionados com êxito ao locatário. Se você não conseguir ver o email, entre em contato com um usuário que já tenha acesso ao locatário e peça para reenviar o convite para você usando as etapas especificadas em [reenviar convites para usuários convidados](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Depois que o email de convite for recebido, você deverá abrir o email e selecionar o link para aceitar o convite. Em seguida, você deve sair do portal do Azure e entrar novamente. (A atualização do navegador não funcionará.) Em seguida, você pode começar a criar o projeto de migração.

## <a name="appliance-issues"></a>Problemas de dispositivo

### <a name="deployment-of-an-azure-migrate-appliance-for-vmware-failed-with-a-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry-error"></a>A implantação de um dispositivo de migração do Azure para VMware falhou com um "o arquivo de manifesto fornecido é inválido: Entrada de manifesto OVF inválida ".

1. Verifique se o arquivo OVA do dispositivo de migração do Azure foi baixado corretamente verificando seu valor de hash. Para obter diretrizes, consulte [preparar VMs VMware para avaliação e migração para o Azure](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Se o valor de hash não corresponder, baixe o arquivo OVA novamente e repita a implantação.
2. Se a implantação ainda falhar e se você estiver usando o VMware vSphere cliente para implantar o arquivo OVF, tente implantá-lo por meio do cliente Web vSphere. Se a implantação ainda falhar, tente usar um navegador da Web diferente.
3. Se você estiver usando o cliente Web vSphere e tentando implantá-lo no vCenter Server 6,5 ou 6,7, tente implantar o OVA diretamente no host ESXi seguindo estas etapas:
   - Conecte-se diretamente ao host ESXi (em vez de vCenter Server) usando o cliente Web (*endereço IP de host*://< >/UI).
   - Vá para**inventário** **doméstico** > .
   - Selecione **arquivo** > **implantar modelo OVF**. Navegue até o OVA e conclua a implantação.
4. Se a implantação ainda falhar, entre em contato com o suporte para migrações para Azure.

### <a name="the-appliance-cant-connect-to-the-internet"></a>O dispositivo não pode se conectar à Internet.

Esse comportamento pode ocorrer quando o computador que você está usando está atrás de um proxy. Certifique-se de fornecer as credenciais de autorização se o proxy precisar delas.
Se você estiver usando qualquer proxy de firewall baseado em URL para controlar a conectividade de saída, certifique-se de adicionar essas URLs necessárias a uma lista de permissões:

- [Avaliação do servidor para VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
- [Avaliação do servidor para Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
- [Migração de servidor para VMware (sem agente)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
- [Migração de servidor para VMware (baseada em agente)](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
- [Migração de servidor para Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Se você estiver usando um proxy de interceptação para se conectar à Internet, deverá importar o certificado de proxy para a VM do dispositivo. Você pode importar o certificado de proxy seguindo as etapas em [dispositivo](https://docs.microsoft.com/azure/migrate/concepts-collector)de migrações para Azure.

### <a name="error-802-date-and-time-synchronization-error"></a>Erro 802: Erro de sincronização de data e hora.

O relógio do servidor pode estar fora de sincronização com a hora atual em mais de cinco minutos. Altere a hora do relógio na VM do coletor para corresponder à hora atual, da seguinte maneira:

1. Abra um prompt de comando de administrador na VM.
2. Para verificar o fuso horário, execute **w32tm/TZ**.
3. Para sincronizar o tempo, execute **w32tm/resync**.


###  <a name="connection-failed-error-unabletoconnecttoserver"></a>Falha na conexão. Erro: UnableToConnectToServer.

Talvez você não consiga se conectar ao vCenter Server *ServerName*. com: 9443. Os detalhes do erro indicam que não há nenhum ponto de extremidadeouvindo em https://servername. com: 9443/SDK que pode aceitar a mensagem.

Nessa situação, verifique se você está executando a versão mais recente do dispositivo coletor. Se você não tiver, atualize o dispositivo para a [versão mais recente](https://docs.microsoft.com/azure/migrate/concepts-collector).

Se o problema ainda ocorrer na versão mais recente, o computador do coletor poderá não conseguir resolver o nome do vCenter Server especificado ou a porta especificada poderá estar errada. Por padrão, se a porta não for especificada, o coletor tentará se conectar ao número da porta 443.

1. Executeping servername. com do computador coletor.
2. Se a etapa 1 falhar, tente se conectar ao servidor do vCenter usando o endereço IP.
3. Identifique o número de porta correto para ligar ao vCenter.
4. Verifique se o servidor do vCenter está em execução.


### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>O dispositivo pode não ser registrado com êxito no projeto de migrações para Azure (ID do erro: 60052).

Esse erro ocorrerá se a conta do Azure usada para registrar o dispositivo não tiver permissões suficientes. Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos permissões de colaborador na assinatura. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as funções e permissões do Azure necessárias.

### <a name="the-appliance-might-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>O dispositivo pode não ser registrado com êxito no projeto de migrações para Azure (ID do erro: 60039).

O projeto de migrações para Azure selecionado para registrar o dispositivo pode não ser encontrado. Nessa situação, o registro falha. Vá para a portal do Azure e verifique se o projeto existe em seu grupo de recursos. Se o projeto não existir, crie um novo projeto de migrações para Azure em seu grupo de recursos e registre o dispositivo novamente. [Saiba mais](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) sobre como criar um novo projeto de migrações para Azure.

### <a name="an-azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Falha em uma operação de gerenciamento de Azure Key Vault (ID do erro: 60030, 60031).

Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos permissões de colaborador na assinatura. Além disso, verifique se a conta tem acesso ao cofre de chaves especificado na mensagem de erro e repita a operação. Se o problema persistir, entre em contato com o suporte da Microsoft. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as funções e permissões do Azure necessárias.

### <a name="discovery-couldnt-be-initiated-because-of-an-error-the-operation-failed-for-the-specified-list-of-hosts-or-clusters-error-id-60028"></a>Não foi possível iniciar a descoberta devido a um erro. A operação falhou para a lista especificada de hosts ou clusters (ID do erro: 60028).

Não foi possível iniciar a descoberta nos hosts listados no erro devido a um problema no acesso ou recuperação de informações da VM. O restante dos hosts foi adicionado com êxito. Adicione os hosts listados no erro novamente usando a opção **Adicionar host** . Se houver um erro de validação, examine as diretrizes de correção para corrigir os erros e tente a opção **salvar e iniciar descoberta** novamente.

### <a name="an-azure-ad-operation-failed-the-error-occurred-while-creating-or-updating-the-azure-ad-application-error-id-60025"></a>Falha em uma operação do Azure AD. O erro ocorreu ao criar ou atualizar o aplicativo do Azure AD (ID do erro: 60025).

Este erro ocorre quando a conta de utilizador do Azure utilizada para iniciar a deteção é diferente da conta utilizada para registar a aplicação. Pode optar por uma das seguintes opções:
1. Verifique se a conta de usuário que está iniciando a descoberta é a mesma usada para registrar o dispositivo.
1. Forneça permissões de acesso de aplicativo do AAD para a outra conta de usuário para a qual a operação de descoberta está falhando.
1. Exclua o grupo de recursos criado anteriormente para o projeto de migrações para Azure e crie outro grupo de recursos para iniciar novamente.

[Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as permissões de aplicativo do AAD.

## <a name="discovery-issues"></a>Problemas de deteção

### <a name="i-started-discovery-but-dont-see-the-discovered-vms-on-the-azure-portal-the-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>Comecei a descoberta, mas não vejo as VMs descobertas no portal do Azure. Os blocos **avaliação do servidor** e migração do **servidor** mostram o status "descoberta em andamento".
Depois de iniciar a descoberta do dispositivo, aguarde algum tempo para que os computadores descobertos sejam exibidos no portal do Azure. Leva cerca de 15 minutos para uma descoberta do VMware e cerca de 2 minutos por host adicionado para uma descoberta do Hyper-V. Se você continuar a ver "descoberta em andamento" mesmo após esses períodos de espera, selecione **Atualizar** na guia **servidores** . Isso deve mostrar a contagem dos servidores descobertos nos blocos avaliação do servidor e migração do servidor.


### <a name="im-using-an-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-shown-in-the-portal"></a>Estou usando um dispositivo que descobre continuamente meu ambiente local, mas as VMs que são excluídas no meu ambiente local ainda são mostradas no Portal.

Leva até 30 minutos para que os dados de descoberta coletados pelo dispositivo sejam refletidos no Portal. Se você não vir informações atualizadas mesmo após 30 minutos, atualize os dados seguindo estas etapas:

1. Vá para **servidores** > **migrações do servidor do Azure**e selecione **visão geral**.
2. Em **gerenciar**, selecione **integridade do agente**
3. Selecione **Atualizar agente**. Você verá essa opção abaixo da lista de agentes.
4. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas em suas VMs.

### <a name="i-dont-see-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Não vejo as informações mais recentes sobre as VMs locais no Portal.

Leva até 30 minutos para que os dados de descoberta coletados pelo dispositivo sejam refletidos no Portal. Se você não vir informações atualizadas mesmo após 30 minutos, atualize os dados seguindo estas etapas:

1. Vá para **servidores** > **migrações do servidor do Azure**e selecione **visão geral**.
2. Em **gerenciar**, selecione **integridade do agente**
3. Selecione **Atualizar agente**. Você verá essa opção abaixo da lista de agentes.
4. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas em suas VMs.

### <a name="cant-connect-to-a-host-or-cluster-because-the-server-name-cant-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Não é possível se conectar a um host ou cluster porque o nome do servidor não pode ser resolvido. Código de erro WinRM: 0x803381B9 (ID do erro: 50004).
Esse erro ocorrerá se o serviço DNS do Azure para o dispositivo não puder resolver o cluster ou o nome do host fornecido. Se você vir esse erro no cluster, tente fornecer o FQDN (nome de domínio totalmente qualificado) do cluster.

Você também pode ver esse erro para hosts em um cluster. Nesse caso, o dispositivo pode se conectar ao cluster, mas o cluster retorna nomes de host que não são FQDNs.

Para resolver esse erro, atualize o arquivo de hosts no dispositivo adicionando um mapeamento do endereço IP e nomes de host:
1. Abra o bloco de notas como administrador. Em seguida, abra o arquivo C:\Windows\System32\Drivers\etc\hosts.
2. Adicione o endereço IP e o nome do host em uma linha. Repita para cada host ou cluster em que você vê esse erro.
3. Guarde e feche o ficheiro de anfitriões.
4. Você pode verificar se o dispositivo pode se conectar aos hosts usando o aplicativo de gerenciamento de dispositivo. Após 30 minutos, você poderá ver as informações mais recentes para esses hosts no portal do Azure.


## <a name="assessment-issues"></a>Problemas de avaliação

### <a name="azure-readiness-issues"></a>Problemas de preparação do Azure

Problema | Remediação
--- | ---
Tipo de arranque não suportado | O Azure não dá suporte a VMs com um tipo de inicialização EFI. Recomendamos que você converta o tipo de inicialização para BIOS antes de executar uma migração. <br/><br/>Você pode usar a migração de servidor de migrações para o Azure para lidar com a migração dessas VMs. Ele converterá o tipo de inicialização da VM para BIOS durante a migração.
Sistema operacional Windows com suporte condicional | O sistema operacional passou sua data de fim de suporte e precisa de um contrato de suporte personalizado (CSA) para [dar suporte no Azure](https://aka.ms/WSosstatement). Considere atualizar o sistema operacional antes de migrar para o Azure.
SO Windows não suportado | O Azure dá suporte apenas a [versões selecionadas do sistema operacional Windows](https://aka.ms/WSosstatement). Considere atualizar o sistema operacional do computador antes de migrar para o Azure.
SO Linux condicionalmente endossado | O Azure endossa apenas [as versões selecionadas do sistema operacional Linux](../virtual-machines/linux/endorsed-distros.md). Considere atualizar o sistema operacional do computador antes de migrar para o Azure.
SO Linux não endossado | O computador pode iniciar no Azure, mas o Azure não fornece suporte ao sistema operacional. Considere atualizar o sistema operacional para uma [versão do Linux endossada](../virtual-machines/linux/endorsed-distros.md) antes de migrar para o Azure.
Sistema operacional desconhecido | O sistema operacional da VM foi especificado como "other" em vCenter Server. Esse comportamento impede que as migrações para Azure verifiquem a prontidão do Azure da VM. Verifique se o sistema operacional do computador tem [suporte](https://aka.ms/azureoslist) no Azure antes de migrar o computador.
Número de bits do SO não suportados | As VMs com um sistema operacional de 32 bits podem ser inicializadas no Azure, mas recomendamos que você atualize o sistema operacional da VM para 64 bits antes de migrar para o Azure.
Requer uma assinatura Microsoft Visual Studio | O computador está executando um sistema operacional cliente Windows, que tem suporte apenas por meio de uma assinatura do Visual Studio.
VM não encontrada para o desempenho de armazenamento necessário | O desempenho de armazenamento (operações de entrada/saída por segundo [IOPS] e taxa de transferência) necessários para o computador excede o suporte à VM do Azure. Reduza os requisitos de armazenamento para a máquina antes da migração.
VM não encontrada para o desempenho de rede necessário | O desempenho de rede (entrada/saída) necessário para o computador excede o suporte de VM do Azure. Reduza os requisitos de rede para o computador.
VM não encontrada no local especificado | Use um local de destino diferente antes da migração.
Um ou mais discos não são adequados | Um ou mais discos anexados à VM não atendem aos requisitos do Azure. Azure Migrate: Atualmente, a avaliação do servidor não dá suporte a discos SSD Ultra e avalia os discos com base nos limites de disco para discos gerenciados Premium (32 TB).  Para cada disco anexado à VM, verifique se o tamanho do disco é < 64 TB (com suporte de discos SSD Ultra), caso contrário, reduza o tamanho do disco antes de migrar para o Azure ou use vários discos no Azure e distribua [-os](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) para obter limites de armazenamento mais altos. Certifique-se de que o desempenho (IOPS e taxa de transferência) necessário para cada disco tem suporte dos [discos de máquina virtual gerenciada](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)pelo Azure.
Um ou mais adaptadores de rede inadequados. | Remova adaptadores de rede não utilizados do computador antes da migração.
A contagem do disco excede o limite | Remova os discos não utilizados do computador antes da migração.
O tamanho do disco excede o limite | Azure Migrate: Atualmente, a avaliação do servidor não dá suporte a discos SSD Ultra e avalia os discos com base nos limites de disco Premium (32 TB). No entanto, o Azure dá suporte a discos com tamanho de até 64 TB (com suporte de discos SSD Ultra). Reduza os discos para menos de 64 TB antes da migração ou use vários discos no Azure e distribua [-os juntos](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping) para obter limites de armazenamento mais altos.
Disco não disponível no local especificado | Verifique se o disco está em seu local de destino antes de migrar.
Disco não disponível para a redundância especificada | O disco deve usar o tipo de armazenamento de redundância definido nas configurações de avaliação (LRS por padrão).
Não foi possível determinar a adequação do disco devido a um erro interno | Tente criar uma nova avaliação para o grupo.
VM com núcleos necessários e memória não encontrada | O Azure não pôde encontrar um tipo de VM adequado. Reduza a memória e o número de núcleos do computador local antes de migrar.
Não foi possível determinar a adequação da VM devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação para um ou mais discos devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação para um ou mais adaptadores de rede devido a um erro interno | Tente criar uma nova avaliação para o grupo.

### <a name="i-cant-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Não é possível especificar Enterprise Agreement (EA) como uma oferta do Azure nas propriedades de avaliação.
Atualmente, a avaliação de servidor de migrações para Azure não oferece suporte a preços baseados em Enterprise Agreement (EA). Para contornar essa limitação, use o **pré-pago** como a oferta do Azure e use a propriedade de **desconto** para especificar qualquer desconto personalizado que você receber. [Saiba mais sobre como pode personalizar uma avaliação](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready"></a>Por que a avaliação do servidor marca minhas VMs Linux "condicionalmente pronta"?
Há uma lacuna conhecida na avaliação do servidor que impede a detecção da versão secundária do SO Linux instalada nas VMs locais (por exemplo, para RHEL 6,10, atualmente a avaliação do servidor detecta apenas RHEL 6 como a versão do sistema operacional). Como o Azure endossa apenas versões específicas do Linux, as VMs do Linux estão atualmente marcadas como prontas para avaliação de servidor. Você pode determinar se o SO Linux em execução na VM local é endossado no Azure examinando a documentação de [suporte do Azure Linux.](https://aka.ms/migrate/selfhost/azureendorseddistros) Depois de verificar a distribuição endossada, você pode ignorar este aviso.

### <a name="why-does-the-vm-sku-recommended-by-server-assessment-have-more-cores-and-more-memory-than-whats-allocated-on-premises"></a>Por que o SKU da VM recomendado pela avaliação do servidor tem mais núcleos e mais memória do que o que está alocado localmente?
A recomendação do SKU da VM na Avaliação do Servidor depende das propriedades de avaliação. Você pode criar dois tipos de avaliações na avaliação do servidor: *Baseado em desempenho* e *como local*. Para avaliações baseadas em desempenho, a avaliação do servidor considera os dados de utilização das VMs locais (CPU, memória, disco e utilização de rede) para determinar o SKU de VM de destino correto para suas VMs locais. Além disso, para o dimensionamento baseado em desempenho, o fator de conforto é considerado na determinação da utilização efetiva. Para o dimensionamento local, os dados de desempenho não são considerados e um SKU de destino é recomendado com base no que é alocado localmente.

Por exemplo, digamos que haja uma VM local com 4 núcleos e 8 GB de memória com 50% de utilização da CPU e 50% de utilização da memória, e um fator de conforto de 1,3 seja especificado na avaliação. Se os critérios de dimensionamento da avaliação forem **locais**, é recomendável um SKU de VM do Azure com 4 núcleos e 8 GIGABYTES (GB) de memória.

No entanto, suponha que os critérios de dimensionamento sejam baseados em desempenho. Com base na utilização efetiva de CPU e memória (50% de 4 núcleos * 1,3 = 2,6 núcleos e 50% de 8 GB de memória * 1,3 = 5,3 GB de memória), o SKU de VM mais barato de quatro núcleos (contagem de núcleos mais próximo com suporte) e 8 GB de memória (tamanho de memória mais próximo com suporte) seria recomendado. [Saiba mais sobre como a Avaliação do Servidor realiza o dimensionamento.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="why-is-the-disk-sku-recommended-by-server-assessment-bigger-than-whats-allocated-on-premises"></a>Por que o SKU de disco recomendado pela avaliação do servidor é maior do que o que está alocado localmente?
O dimensionamento de disco na avaliação do servidor depende de duas propriedades de avaliação: critérios de dimensionamento e tipo de armazenamento. Se os critérios de dimensionamento forem **baseados em desempenho** e o tipo de armazenamento estiver definido como **automático**, os valores de IOPS e taxa de transferência do disco serão considerados para identificar o tipo de disco de destino (HDD Standard, SSD Standard ou discos Premium). Um SKU de disco do tipo de disco é recomendado, e essa recomendação considera os requisitos de tamanho do disco local. Se os critérios de dimensionamento forem **baseados em desempenho**e o tipo de armazenamento for **Premium**, um SKU de disco Premium no Azure será recomendado com base nos requisitos de IOPS, taxa de transferência e tamanho do disco local. A mesma lógica é usada para executar o dimensionamento de disco quando os critérios de dimensionamento são **locais** e o tipo de armazenamento é **HDD Standard**, **SSD Standard**ou **Premium**.

Por exemplo, se você tiver um disco local com 32 GB de memória, mas o IOPS agregado de leitura e gravação para o disco for de 800 IOPS, a avaliação do servidor recomendará um tipo de disco Premium (devido aos requisitos de IOPS mais altos) e, em seguida, recomendará um SKU de disco que possa dar suporte o IOPS e o tamanho necessários. A correspondência mais próxima neste exemplo seria P15 (256 GB, IOPS de 1100). Embora o tamanho exigido pelo disco local tenha 32 GB, a avaliação do servidor recomenda um disco maior devido ao requisito de IOPS alto do disco local.

### <a name="why-does-my-assessment-report-indicate-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Por que meu relatório de avaliação indica "PercentageOfCoresUtilizedMissing" ou "PercentageOfMemoryUtilizedMissing" para algumas VMs?
Esses problemas são relatados quando o dispositivo de migrações para Azure não pode coletar dados de desempenho para as VMs locais. Esse comportamento pode acontecer se as VMs estiverem desativadas durante a criação da avaliação (último dia/uma semana/um mês). O dispositivo não pode coletar dados de desempenho para uma VM quando ela está desativada. Se apenas os contadores de memória estiverem ausentes e você estiver tentando avaliar as VMs do Hyper-V, verifique se você tem memória dinâmica habilitada nessas VMs. Há um problema conhecido em que um dispositivo de migrações para Azure não pode coletar dados de utilização de memória para VMs que não têm memória dinâmica habilitada. Esse problema afeta apenas as VMs do Hyper-V, não as VMs do VMware. Se algum dos contadores de desempenho estiver ausente, a avaliação do servidor de migrações para Azure voltará para os núcleos e a memória alocados e recomendará um tamanho de VM correspondente.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>O custo de licença do sistema operacional da VM está incluído no custo de computação estimado pela avaliação do servidor?
A avaliação do servidor atualmente considera o custo de licença do sistema operacional somente para computadores Windows. Os custos de licença do so para computadores Linux não são atualmente considerados.

### <a name="what-impact-do-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Que impacto o histórico de desempenho e a utilização de percentil têm sobre as recomendações de tamanho?
Essas propriedades se aplicam somente ao dimensionamento baseado em desempenho. A Avaliação do Servidor recolhe continuamente os dados de desempenho das máquinas no local e utiliza-os para recomendar o SKU da VM e o SKU do disco no Azure. Esses dados de desempenho são coletados pela avaliação do servidor da seguinte maneira:
- O dispositivo de migração do Azure cria o perfil continuamente no ambiente local para coletar dados de utilização em tempo real a cada 20 segundos para VMs VMware e a cada 30 segundos para VMs do Hyper-V.
- O dispositivo acumula os exemplos de 20 segundos e 30 segundos para criar um único ponto de dados para cada 10 minutos. Para criar o ponto de dados único, o dispositivo seleciona o valor de pico de todas as amostras de 20 segundos e 30 segundos e, em seguida, envia-o para o Azure.
- Quando cria uma avaliação na Avaliação do Servidor, com base na duração do desempenho e no valor de percentil do histórico de desempenho, o valor de utilização representativo é identificado. Por exemplo, se o histórico de desempenho for uma semana e a utilização de percentil for 95 º, as migrações para Azure classificarão todos os pontos de exemplo de 10 minutos para a última semana em ordem crescente e, em seguida, selecionará o 95 º percentil como o valor representativo.
O valor do 95 º percentil garante que você ignore quaisquer exceções, que poderão ser incluídas se você escolher o 99 º percentil. Se você quiser escolher o pico de uso do período e não quiser perder as exceções, deverá selecionar o 99 º percentil para utilização de percentil.

## <a name="dependency-visualization-issues"></a>Problemas de visualização de dependência

### <a name="i-cant-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Não consigo encontrar a funcionalidade de visualização de dependência para projetos do Azure governamental.

As migrações para Azure dependem Mapa do Serviço para a funcionalidade de visualização de dependência. Como Mapa do Serviço não está disponível no Azure governamental no momento, essa funcionalidade não está disponível no Azure governamental.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Instalei o Microsoft Monitoring Agent (MMA) e o Dependency Agent em minhas VMs locais, mas as dependências agora aparecem no portal de migrações para Azure.

Depois de instalar os agentes, as migrações para Azure normalmente levarão de 15-30 minutos para exibir as dependências no Portal. Se você tiver aguardado por mais de 30 minutos, verifique se MMA pode se comunicar com o espaço de trabalho do OMS seguindo estas etapas.

Para VM do Windows:
1. Vá para o painel de controle e inicie Microsoft Monitoring Agent.
2. Na guia **log Analytics do Azure (OMS)** na caixa de diálogo **Propriedades do Microsoft Monitoring Agent** , verifique se o **status** do espaço de trabalho está verde.
3. Se o status não for verde, tente remover o espaço de trabalho e adicioná-lo novamente ao MMA.

      ![Caixa de diálogo Propriedades de MMA](./media/troubleshooting-general/mma-status.png)

Para a VM do Linux, verifique se os comandos de instalação do MMA e do agente de dependência foram bem-sucedidos.

### <a name="what-operating-systems-does-mma-support"></a>A quais sistemas operacionais o MMA dá suporte?

 [Aqui está uma lista de sistemas operacionais Windows com suporte do MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
E [aqui está uma lista de sistemas operacionais Linux com suporte do MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-operating-systems-does-the-dependency-agent-support"></a>A quais sistemas operacionais o agente de dependência oferece suporte?

[Aqui está uma lista de [sistemas operacionais Windows e Linux aos quais Azure monitor para VMs dá suporte](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems).

### <a name="i-cant-visualize-dependencies-in-azure-migrate-for-more-than-a-one-hour-duration"></a>Não consigo visualizar dependências em migrações do Azure por mais de uma duração de uma hora.
Nas migrações para Azure, você pode visualizar dependências por até uma duração de uma hora. Embora as migrações para Azure permitam que você volte para uma data específica no último mês, a duração máxima para a qual você pode visualizar as dependências é de uma hora.

Por exemplo, você pode usar a funcionalidade duração de tempo no mapa de dependências para exibir dependências de ontem, mas você pode exibi-las apenas por um período de uma hora. No entanto, você pode usar os logs de Azure Monitor para [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração maior.

### <a name="i-cant-visualize-dependencies-for-groups-that-have-more-than-10-vms"></a>Não consigo visualizar dependências de grupos que têm mais de 10 VMs.
Você pode [Visualizar dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) que têm até 10 VMS, se você tiver um grupo com mais de 10 VMS, recomendamos que você divida o grupo em grupos menores e visualize as dependências.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Instalei os agentes e usei a visualização de dependência para criar grupos. Agora, após o failover, os computadores mostram a ação "instalar agente" em vez de "Exibir dependências".
* Após um failover planejado ou não planejado, as máquinas locais são desativadas e as máquinas equivalentes são giradas no Azure. Esses computadores adquirem um endereço MAC diferente. Eles podem adquirir um endereço IP diferente com base no fato de o usuário optar por manter um endereço IP local.

  Se os endereços MAC e IP forem diferentes, as migrações para Azure não associarão os computadores locais a nenhum dado de dependência Mapa do Serviço. Em vez disso, ele solicita que o usuário instale agentes em vez de exibir dependências.
* Failover pós-teste, os computadores locais permanecem ligados conforme o esperado. As máquinas equivalentes giradas no Azure adquirem um endereço MAC diferente e podem adquirir endereços IP diferentes. A menos que o usuário bloqueie o tráfego de log de Azure Monitor de saída desses computadores, as migrações para Azure não associam os computadores locais a qualquer Mapa do Serviço dados de dependência e solicitam que os usuários instalem agentes em vez de exibir dependências.

## <a name="collect-azure-portal-logs"></a>Coletar logs de portal do Azure

**Como fazer coletar portal do Azure logs de tráfego de rede?**

1. Abra o navegador, vá [para o portal](https://portal.azure.com)e entre.
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
