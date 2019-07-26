---
title: Solucionar problemas de migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos problemas conhecidos no serviço de migrações para Azure e dicas de solução de erros comuns.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: raynew
ms.openlocfilehash: 15d3809b9a028fd2495c504e9bf19251dd051520
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372581"
---
# <a name="troubleshoot-azure-migrate"></a>Resolver problemas do Azure Migrate

As migrações para [Azure](migrate-services-overview.md) fornecem um hub de ferramentas da Microsoft para avaliação e migração, bem como ofertas de ISVs (fornecedores independentes de software) de terceiros. Este documento fornece ajuda sobre como solucionar erros com as migrações para Azure, migrações para Azure: Avaliação do servidor e migrações para Azure: Migração do Servidor.

## <a name="azure-migrate-project-issues"></a>Problemas do projeto de migrações para Azure

### <a name="i-am-unable-to-find-my-existing-azure-migrate-project"></a>Não consigo encontrar meu projeto existente de migrações para Azure.

Há [duas versões](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) de migrações para Azure. Dependendo da versão na qual você criou o projeto, siga as etapas abaixo para localizar o projeto:

1. Se você estiver procurando um projeto criado com a versão anterior (experiência antiga) das migrações para Azure, siga as etapas abaixo para localizar o projeto.

    1. Vá para [portal do Azure](https://portal.azure.com), procure migrações para **Azure**.
    2. No painel migrações para Azure, você verá uma faixa que fala sobre o acesso a projetos mais antigos. Você verá essa faixa somente se tiver criado um projeto com a experiência antiga. Clique na faixa.

    ![Acessar projetos existentes](./media/troubleshooting-general/access-existing-projects.png)

    3. Agora, você verá a lista de projetos existentes criados com a versão anterior do migrações para Azure.

2. Se você estiver procurando um projeto criado com a versão atual (nova experiência), siga as etapas abaixo para localizar o projeto.

    1. Vá para [portal do Azure](https://portal.azure.com), procure migrações para **Azure**.
    2. No painel migrações para Azure, vá para a página **servidores** no painel esquerdo e selecione **alterar** no canto superior direito:

    ![Alternar para um projeto atual de migrações para Azure](./media/troubleshooting-general/switch-project.png)

    3. Selecione a **assinatura** apropriada e **migre o projeto**.

### <a name="i-am-unable-to-create-a-second-azure-migrate-project"></a>Não consigo criar um segundo projeto de migrações para Azure.

Siga as etapas abaixo para criar um novo projeto de migrações para Azure.

1. Vá para [portal do Azure](https://portal.azure.com), procure migrações para **Azure**.
2. No painel migrações para Azure, vá para a página **servidores** no painel esquerdo e selecione **alterar** no canto superior direito:

   ![Alterar projeto de migrações para Azure](./media/troubleshooting-general/switch-project.png)

3. Para criar um novo projeto, selecione **clique aqui** , conforme mostrado na captura de tela abaixo:

   ![Criar um segundo projeto de migrações para Azure](./media/troubleshooting-general/create-new-project.png)

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Quais geografias do Azure são compatíveis com as migrações para Azure?

Você pode encontrar a lista para [VMware aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) e para o [Hyper-V aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Exclusão de projetos de migrações para Azure e espaço de trabalho Log Analytics associado

Quando você exclui um projeto de migrações para Azure, ele exclui o projeto de migração junto com os metadados sobre computadores descobertos. No entanto, se você anexou um espaço de trabalho Log Analytics à ferramenta de avaliação do servidor, ele não excluirá automaticamente o espaço de trabalho Log Analytics. Isso ocorre porque o mesmo espaço de trabalho Log Analytics pode ser usado para vários casos de uso. Se você quiser excluir o espaço de trabalho Log Analytics também, será necessário fazer isso manualmente.

1. Navegue até o espaço de trabalho Log Analytics anexado ao projeto.
     1. Se você ainda não tiver excluído o projeto de migração, poderá encontrar o link para o espaço de trabalho na página Visão geral da avaliação do servidor na seção Essentials.

     ![Espaço de trabalho da LA](./media/troubleshooting-general/loganalytics-workspace.png)

     2. Se você já tiver excluído o projeto de migração, selecione **grupos de recursos** no painel esquerdo na portal do Azure. Vá para o grupo de recursos no qual o espaço de trabalho foi criado e navegue até ele.
2. Siga as instruções [neste artigo](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) para excluir o espaço de trabalho.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Falha na criação do projeto de migração com solicitações de erro *devem conter cabeçalhos de identidade do usuário*

Esse problema pode ocorrer para usuários que não têm acesso ao locatário do Azure Active Directory (Azure AD) da organização. Quando um usuário é adicionado a um locatário do Azure AD pela primeira vez, ele recebe um convite por email para ingressar no locatário. Os usuários precisam acessar o email e aceitar o convite para ser adicionado com êxito ao locatário. Se não for possível ver o email, entre em contato com um usuário que já tenha acesso ao locatário e peça para reenviar o convite para você usando as etapas especificadas [aqui](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Depois que o email de convite for recebido, você precisará abrir o email e clicar no link no email para aceitar o convite. Quando isso for feito, você precisará sair do portal do Azure e entrar novamente, a atualização do navegador não funcionará. Em seguida, você pode tentar criar o projeto de migração.

## <a name="appliance-issues"></a>Problemas de dispositivo

### <a name="deployment-of-azure-migrate-appliance-for-vmware-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Falha na implantação do dispositivo de migração do Azure para VMware com o erro: O ficheiro de manifesto fornecido é inválido: Entrada de manifesto OVF inválida.

1. Verifique se o arquivo OVA do dispositivo de migração do Azure foi baixado corretamente verificando seu valor de hash. Consulte o [artigo](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware) para verificar o valor de hash. Se o valor de hash não for correspondente, baixe o arquivo OVA novamente e repita a implantação.
2. Se ainda assim falhar e se estiver a utilizar o Cliente VMware vSphere para implementar o OVF, tente implementá-lo por meio do Cliente Web vSphere. Se ainda falhar, tente usar um navegador da Web diferente.
3. Se você estiver usando o cliente Web vSphere e tentar implantá-lo no vCenter Server 6,5 ou 6,7, tente implantar o OVA diretamente no host ESXi seguindo as etapas abaixo:
   - Conecte-se diretamente ao host ESXi (em vez de vCenter Server) usando o cliente Web (*endereço IP do host*do https://< >/UI).
   - Vá para**inventário** **doméstico** > .
   - Clique em **arquivo** > **implantar modelo de OVF**. Navegue até o OVA e conclua a implantação.
4. Se a implantação ainda falhar, entre em contato com o suporte para migrações para Azure.

### <a name="appliance-is-not-able-to-connect-to-the-internet"></a>O dispositivo não é capaz de se conectar à Internet

Isso pode acontecer quando o computador que você está usando está atrás de um proxy. Certifique-se de fornecer as credenciais de autorização se o proxy precisar de uma.
Se você estiver usando qualquer proxy de firewall baseado em URL para controlar a conectividade de saída, certifique-se de adicionar essas URLs necessárias a uma lista de permissões:

Cenário | Lista de URLs
--- | ---
Avaliação do servidor para VMware | [Aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
Avaliação do servidor para Hyper-V | [Aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
Migração de servidor para VMware (sem agente) | [Aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
Migração de servidor para VMware (baseada em agente) | [Aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
Migração de servidor para Hyper-V | [Aqui](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

Se você estiver usando um proxy de interceptação para se conectar à Internet, será necessário importar o certificado de proxy para a VM do dispositivo. Você pode importar o certificado de proxy usando as etapas detalhadas [aqui](https://docs.microsoft.com/azure/migrate/concepts-collector).

### <a name="error-802-date-and-time-synchronization-error"></a>Erro 802: Erro de sincronização de data e hora

O relógio do servidor pode estar fora de sincronia com a hora atual em mais de cinco minutos. Altere a hora do relógio na VM do coletor para corresponder à hora atual, da seguinte maneira:

1. Abra um prompt de comando de administrador na VM.
2. Para verificar o fuso horário, execute w32tm/TZ.
3. Para sincronizar o tempo, execute w32tm/resync.


### <a name="error-unabletoconnecttoserver"></a>Error UnableToConnectToServer

Não é possível ligar ao vCenter Server "Servername.com:9443" devido ao erro: Não havia um ponto final preparado em https://Servername.com:9443/sdk que pudesse aceitar a mensagem.

Verifique se você está executando a versão mais recente do dispositivo coletor, caso contrário, atualize o dispositivo para a [versão mais recente](https://docs.microsoft.com/azure/migrate/concepts-collector).

Se o problema ainda ocorrer na versão mais recente, pode ser porque o computador coletor não pode resolver o nome de vCenter Server especificado ou a porta especificada está errada. Por padrão, se a porta não for especificada, o coletor tentará se conectar ao número da porta 443.

1. Tente executar ping no Servername.com do computador coletor.
2. Se o passo 1 falhar, tente ligar ao servidor do vCenter através do endereço IP.
3. Identifique o número de porta correto para ligar ao vCenter.
4. Por fim, verifique se o servidor do vCenter está em execução.


### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60052"></a>O dispositivo não pôde ser registrado com êxito no projeto de migrações para Azure (ID do erro: 60052)

Esse erro ocorre devido a permissões insuficientes na conta do Azure usadas para registrar o dispositivo. Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos o acesso ' colaborador ' na assinatura. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as funções e permissões do Azure necessárias.

### <a name="the-appliance-could-not-be-registered-successfully-to-the-azure-migrate-project-error-id-60039"></a>O dispositivo não pôde ser registrado com êxito no projeto de migrações para Azure (ID do erro: 60039)

O projeto de migrações para Azure selecionado por você para registrar o dispositivo não foi encontrado, fazendo com que o registro falhe. Vá para a portal do Azure e verifique se o projeto existe em seu grupo de recursos. Se o projeto não existir, crie um novo projeto de migrações para Azure em seu grupo de recursos e registre o dispositivo novamente. [Saiba mais](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) sobre como criar um novo projeto de migrações para Azure.

### <a name="azure-key-vault-management-operation-failed-error-id-60030-60031"></a>Falha na operação de gerenciamento do cofre de chaves do Azure (ID do erro: 60030, 60031)

Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos o acesso ' colaborador ' na assinatura. Além disso, verifique se a conta tem acesso ao Key Vault especificado na mensagem de erro e repita a operação. Se o problema persistir, entre em contato com o suporte da Microsoft. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as funções e permissões do Azure necessárias.

### <a name="discovery-could-not-be-initiated-due-to-the-error-the-operation-failed-for-the-given-list-of-hosts-or-clusters-error-id-60028"></a>Não foi possível iniciar a descoberta devido ao erro. A operação falhou para a lista determinada de hosts ou clusters (ID do erro: 60028)

Não foi possível iniciar a descoberta nos hosts listados no erro devido a um problema no acesso ou recuperação de informações da VM; o restante dos hosts que você adicionou foi adicionado com êxito. Adicione novamente os hosts no erro usando a opção **Adicionar host** . Se houver um erro de validação, examine as diretrizes de correção para corrigir os erros e tente **salvar e iniciar a descoberta** novamente.

### <a name="azure-active-directory-aad-operation-failed-the-error-occurred-while-creating-or-updating-the-aad-application-error-id-60025"></a>Falha na operação do Azure Active Directory (AAD). O erro ocorreu ao criar ou atualizar o aplicativo AAD (ID do erro: 60025)

A conta de usuário do Azure usada para registrar o dispositivo não tem acesso ao aplicativo AAD especificado na mensagem de erro. Verifique se você é o proprietário do aplicativo do AAD. [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as permissões de aplicativo do AAD.


## <a name="discovery-issues"></a>Problemas de deteção

### <a name="i-started-discovery-but-i-dont-see-the-discovered-vms-on-azure-portal-server-assessment-and-server-migration-tiles-show-a-status-of-discovery-in-progress"></a>Comecei a descoberta, mas não vejo as VMs descobertas em portal do Azure. Os blocos avaliação do servidor e migração do servidor mostram o status "descoberta em andamento"
Depois de iniciar a descoberta do dispositivo, aguarde algum tempo para que os computadores descobertos apareçam no portal do Azure. Leva cerca de 15 minutos para uma descoberta do VMware e cerca de 2 minutos por host adicionado para uma descoberta do Hyper-V. Se você continuar a ver "descoberta em andamento" mesmo após esse período, clique em **Atualizar** na guia **servidores** . Isso deve mostrar a contagem dos servidores descobertos nos blocos avaliação do servidor e migração do servidor.


### <a name="i-am-using-the-appliance-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Estou usando o dispositivo que descobre continuamente o meu ambiente local, mas as VMs que são excluídas no meu ambiente local ainda estão sendo mostradas no Portal.

Leva até 30 minutos para que os dados de descoberta sejam coletados pelo dispositivo para refletir no Portal. Se você não vir informações atualizadas mesmo após 30 minutos, emita uma atualização dos dados usando as seguintes etapas:

1. Em servidores > migrações para Azure: Avaliação do servidor, clique em **visão geral**.
2. Em **gerenciar**, clique em **integridade do agente**
3. Clique na opção para **atualizar o agente**. Você verá essa opção abaixo da lista de agentes.
4. Aguarde a conclusão da operação de atualização. Verifique se você consegue ver informações atualizadas em suas VMs.

### <a name="i-dont-the-latest-information-on-the-on-premise-vms-on-the-portal"></a>Não as informações mais recentes sobre as VMs locais no portal

Leva até 30 minutos para que os dados de descoberta sejam coletados pelo dispositivo para refletir no Portal. Se você não vir informações atualizadas mesmo após 30 minutos, emita uma atualização dos dados usando as seguintes etapas:

1. Em servidores > migrações para Azure: Avaliação do servidor, clique em **visão geral**.
2. Em **gerenciar**, clique em **integridade do agente**
3. Clique na opção para **atualizar o agente**. Você verá essa opção abaixo da lista de agentes.
4. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas em suas VMs.

### <a name="unable-to-connect-to-hosts-or-cluster-as-the-server-name-cannot-be-resolved-winrm-error-code-0x803381b9-error-id-50004"></a>Não é possível se conectar a host (s) ou cluster, pois o nome do servidor não pode ser resolvido. Código de erro WinRM: 0x803381B9 (ID do erro: 50004)
Esse erro ocorre se o DNS que atende o dispositivo não puder resolver o cluster ou o nome do host fornecido. Se você vir esse erro no cluster, tente fornecer o nome de domínio totalmente qualificado do cluster.

Você também poderá ver esse erro para hosts em um cluster. Nesse caso, o dispositivo pode se conectar ao cluster. Mas o cluster retornou os nomes de host que não são nomes de domínio totalmente qualificados.

Para resolver esse erro, atualize o arquivo de hosts no dispositivo adicionando um mapeamento do endereço IP e nomes de host.
1. Abra o bloco de notas como usuário administrador. Abra o arquivo C:\Windows\System32\Drivers\etc\hosts.
2. Adicione o endereço IP e o nome do host em uma linha. Repita para cada host ou cluster em que você vê esse erro.
3. Salve e feche o arquivo de hosts.
4. Você pode verificar se o dispositivo pode se conectar aos hosts usando o aplicativo de gerenciamento de dispositivo. Após 30 minutos, você poderá ver as informações mais recentes sobre esses hosts no portal do Azure.


## <a name="assessment-issues"></a>Problemas de avaliação

### <a name="azure-readiness-issues"></a>Problemas de preparação do Azure

Problema | Remediação
--- | ---
Tipo de arranque não suportado | O Azure não oferece suporte a VMs com o tipo de inicialização EFI. Recomendamos que você converta o tipo de inicialização para BIOS antes de executar uma migração. <br/><br/>Você pode usar a migração de servidor de migrações para Azure para fazer a migração dessas VMs, pois ela converterá o tipo de inicialização da VM no BIOS durante a migração.
Sistema operacional Windows com suporte condicional | O sistema operacional passou sua data de fim de suporte e precisa de um contrato de suporte personalizado (CSA) para [dar suporte no Azure](https://aka.ms/WSosstatement), considere atualizar o sistema operacional antes de migrar para o Azure.
SO Windows não suportado | O Azure dá suporte apenas a [versões do sistema operacional Windows selecionadas](https://aka.ms/WSosstatement), considere atualizar o sistema operacional do computador antes de migrar para o Azure.
SO Linux condicionalmente endossado | O Azure endossa apenas [as versões selecionadas do sistema operacional Linux](../virtual-machines/linux/endorsed-distros.md), considere atualizar o sistema operacional do computador antes de migrar para o Azure.
SO Linux não endossado | O computador pode ser inicializado no Azure, mas nenhum suporte do sistema operacional é fornecido pelo Azure, considere atualizar o sistema operacional para uma [versão do Linux endossada](../virtual-machines/linux/endorsed-distros.md) antes de migrar para o Azure
Sistema operacional desconhecido | O sistema operacional da VM foi especificado como ' Other ' no vCenter Server, devido a qual migrações para Azure não podem identificar a prontidão do Azure da VM. Verifique se o sistema operacional em execução dentro do computador tem [suporte](https://aka.ms/azureoslist) do Azure antes de migrar o computador.
Número de bits do SO não suportados | As VMs com o sistema operacional de 32 bits podem ser inicializadas no Azure, mas é recomendável atualizar o sistema operacional da VM de 32 bits para 64-bit antes de migrar para o Azure.
Requer assinatura do Visual Studio. | O computador tem um sistema operacional cliente Windows em execução dentro dele, que tem suporte apenas na assinatura do Visual Studio.
VM não encontrada para o desempenho de armazenamento necessário. | O desempenho de armazenamento (IOPS/taxa de transferência) necessário para o computador excede o suporte de VM do Azure. Reduza os requisitos de armazenamento para a máquina antes da migração.
VM não encontrada para o desempenho de rede necessário. | O desempenho de rede (entrada/saída) necessário para o computador excede o suporte de VM do Azure. Reduza os requisitos de rede para o computador.
A VM não foi encontrada no local especificado. | Use um local de destino diferente antes da migração.
Um ou mais discos inadequados. | Um ou mais discos anexados à VM não atendem aos requisitos do Azure. Para cada disco anexado à VM, verifique se o tamanho do disco é < 4 TB, caso contrário, reduza o tamanho do disco antes de migrar para o Azure. Verifique se o desempenho (IOPS/taxa de transferência) necessário para cada disco tem suporte dos [discos da máquina virtual gerenciada](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits)pelo Azure.   
Um ou mais adaptadores de rede inadequados. | Remova adaptadores de rede não utilizados do computador antes da migração.
A contagem do disco excede o limite | Remova os discos não utilizados do computador antes da migração.
O tamanho do disco excede o limite | O Azure dá suporte a discos com até 4 TB de tamanho. Reduza os discos para menos de 4 TB antes da migração.
Disco indisponível na localização especificada | Verifique se o disco está em seu local de destino antes de migrar.
Disco indisponível para a redundância especificada | O disco deve usar o tipo de armazenamento de redundância definido nas configurações de avaliação (LRS por padrão).
Não foi possível determinar a adequação do disco devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi encontrada uma VM com os núcleos e memória necessários | O Azure não pôde obter um tipo de VM adequado. Reduza a memória e o número de núcleos do computador local antes de migrar.
Não foi possível determinar a adequação da VM devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação para um ou mais discos devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação para um ou mais adaptadores de rede devido a um erro interno. | Tente criar uma nova avaliação para o grupo.

### <a name="i-am-unable-to-specify-enterprise-agreement-ea-as-an-azure-offer-in-the-assessment-properties"></a>Não consigo especificar Enterprise Agreement (EA) como uma oferta do Azure nas propriedades de avaliação?
Azure Migrate: Atualmente, a Avaliação do Servidor não suporta preços baseados em Contratos Enterprise (EA). A solução alternativa é utilizar "Pay As You Go" como oferta do Azure, assim como a propriedade "Desconto" para especificar qualquer desconto personalizado recebido. [Saiba mais sobre como pode personalizar uma avaliação](https://aka.ms/migrate/selfhelp/eapricing).

### <a name="why-does-server-assessment-mark-my-linux-vms-conditionally-ready-is-there-anything-i-need-to-do-to-fix-this"></a>Por que a avaliação do servidor marca minhas VMs Linux de forma condicional. Há algo que preciso fazer para corrigir isso?
Existe uma lacuna conhecida na Avaliação do Servidor que a impede de detetar a versão secundária do SO Linux instalado nas VMs no local (por exemplo, para RHEL 6.10, atualmente, a Avaliação do Servidor deteta apenas RHEL 6 como a versão do SO). Uma vez que o Azure apoia apenas versões específicas do Linux, as VMs do Linux são atualmente marcadas como condicionalmente preparadas na Avaliação do Servidor. Você pode garantir manualmente se o sistema operacional Linux em execução na VM local é endossado no Azure, revisando a [documentação de suporte do Linux do Azure.](https://aka.ms/migrate/selfhost/azureendorseddistros) Assim que tiver confirmado a distro aprovada, pode ignorar este aviso.

### <a name="the-vm-sku-recommended-by-server-assessment-has-more-number-of-cores-and-a-larger-memory-size-than-what-is-allocated-on-premises-why-is-that-so"></a>A SKU de VM recomendada pela avaliação do servidor tem mais de núcleos e um tamanho de memória maior do que o que é alocado localmente. Por que isso?
A recomendação do SKU da VM na Avaliação do Servidor depende das propriedades de avaliação. Pode criar dois tipos de avaliações na Avaliação do Servidor: avaliações "com base no desempenho" e "como no local". Para avaliações baseadas em desempenho, a avaliação do servidor considera os dados de utilização das VMs locais (CPU, memória, disco e utilização de rede) para determinar o SKU de VM de destino correto para suas VMs locais. Além disso, para o dimensionamento com base no desempenho, o fator de conforto é tido em consideração para identificar a utilização eficaz. No dimensionamento como no local, os dados de desempenho não são considerados e é recomendado um SKU de destino com base no que está alocado no local.

Por exemplo, digamos que haja uma VM local com 4 núcleos e 8 GB de memória com 50% de utilização da CPU e 50% de utilização da memória, e um fator de conforto de 1,3 seja especificado na avaliação. No entanto, se o critério de dimensionamento da avaliação for ' como local ', um SKU de VM do Azure com 4 núcleos e 8 GB de memória será recomendado, no entanto, se o critério de dimensionamento for baseado em desempenho, com base na utilização efetiva de CPU e memória (50% de 4 núcleos * 1,3 = 2,6 núcleos e 50% de 8 GB memória * 1,3 = 5,3-GB de memória), o SKU de VM mais barato de quatro núcleos (contagem de núcleos mais próximo com suporte) e o tamanho de memória de 8 GB (tamanho de memória mais próximo com suporte) seriam recomendados. [Saiba mais sobre como a Avaliação do Servidor realiza o dimensionamento.](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing).

### <a name="the-disk-sku-recommended-by-server-assessment-has-a-bigger-size-than-what-is-allocated-on-premises-why-is-that-so"></a>O SKU de disco recomendado pela avaliação do servidor tem um tamanho maior do que o que é alocado localmente. Por que isso?
O dimensionamento do disco na Avaliação do Servidor depende de duas propriedades de avaliação: o critério de dimensionamento e o tipo de armazenamento. Se o critério de dimensionamento for "baseado em desempenho" e o tipo de armazenamento estiver definido como "automático", os valores de IOPS e taxa de transferência do disco serão considerados para identificar o tipo de disco de destino (HDD Standard, SSD Standard ou discos Premium). Desta forma, é recomendado um SKU de disco no tipo de disco, tendo em consideração os requisitos de tamanho do disco no local. Se o critério de dimensionamento for "baseado em desempenho" e o tipo de armazenamento for "Premium", um SKU de disco Premium no Azure será recomendado com base nos requisitos de IOPS, taxa de transferência e tamanho do disco local. É utilizada a mesma lógica para o dimensionamento do disco quando o critério de dimensionamento é "Como no local"e o tipo de armazenamento é "HDD Standard", "SSD Standard" ou "Premium".

Por exemplo, se você tiver um disco local com memória de 32 GB, mas o IOPS agregado de leitura e gravação para o disco for de 800 IOPS, a avaliação do servidor recomendará um tipo de disco Premium (devido aos requisitos de IOPS mais altos) e, em seguida, recomendará uma SKU de disco, que pode dar suporte ao IOPS e tamanho necessários. A correspondência mais próxima neste exemplo seria P15 (256 GB, IOPS de 1100). Portanto, apesar de o tamanho necessário para o disco no local ser de 32 GB, a Avaliação do Servidor recomendou um disco com um tamanho superior devido ao requisito de IOPS elevado do disco no local.

### <a name="why-does-my-assessment-report-say-percentageofcoresutilizedmissing-or-percentageofmemoryutilizedmissing-for-some-vms"></a>Por que meu relatório de avaliação diz "PercentageOfCoresUtilizedMissing" ou "PercentageOfMemoryUtilizedMissing" para algumas VMs?
Os problemas acima são listados quando o dispositivo de migrações para Azure não pode coletar dados de desempenho para as VMs locais. Isso pode acontecer se as VMs estiverem desligadas durante o período durante o qual você está criando a avaliação (último dia/uma semana/um mês), uma vez que o dispositivo não pode coletar dados de desempenho para uma VM, quando ela estiver desligada. Se apenas os contadores de memória estiverem ausentes e você estiver tentando avaliar as VMs do Hyper-V, verifique se você tem memória dinâmica habilitada nessas VMs. Há um problema conhecido atualmente devido ao qual o dispositivo de migração do Azure não pode coletar a utilização de memória para VMs que não têm memória dinâmica habilitada. Observe que o problema é apenas para VMs do Hyper-V e não para VMs do VMware. Se algum dos contadores de desempenho estiver ausente, migrações para Azure: A avaliação do servidor volta para os núcleos/memória alocados e recomenda um tamanho de VM de acordo.

### <a name="is-the-os-license-cost-of-the-vm-included-in-the-compute-cost-estimated-by-server-assessment"></a>O custo de licença do sistema operacional da VM está incluído no custo de computação estimado pela avaliação do servidor?
De momento, a Avaliação do Servidor considera apenas o custo da licença do SO para máquinas Windows. O custo da licença do SO para máquinas Linux não é considerado atualmente.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Que impacto o histórico de desempenho e a utilização de percentil têm nas recomendações de tamanho?
Estas propriedades só são aplicáveis ao dimensionamento "Com base no desempenho". A Avaliação do Servidor recolhe continuamente os dados de desempenho das máquinas no local e utiliza-os para recomendar o SKU da VM e o SKU do disco no Azure. Observe abaixo a forma como os dados de desempenho são recolhidos pela Avaliação do Servidor:
- O dispositivo de migração do Azure cria o perfil continuamente no ambiente local para coletar dados de utilização em tempo real a cada 20 segundos para VMs VMware e a cada 30 segundos para VMs do Hyper-V.
- A aplicação acumula os exemplos de 20/30 segundos para criar um ponto de dados individual para cada 10 minutos. Para criar o ponto de dados individual, a aplicação seleciona o valor de pico de todos os exemplos de 20/30 segundos e envia-o para o Azure.
- Quando cria uma avaliação na Avaliação do Servidor, com base na duração do desempenho e no valor de percentil do histórico de desempenho, o valor de utilização representativo é identificado. Por exemplo, se o histórico de desempenho for uma semana e a utilização do percentil for 95 º, as migrações para Azure classificarão todos os pontos de exemplo de 10 minutos da última semana em ordem crescente e, em seguida, selecionaremos o 95 º percentil como o valor representativo.
O valor de percentil 95 garante que está a ignorar todos os valores atípicos, que podem ser incluídos se escolher o percentil 99. Se quiser escolher a utilização de pico para o período e não quiser perder quaisquer valores atípicos, deve selecionar o percentil 99 como a utilização de percentil.

## <a name="dependency-visualization-issues"></a>Problemas de visualização de dependência

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Não consigo encontrar a funcionalidade de visualização de dependência para projetos do Azure governamental.

As migrações para Azure dependem Mapa do Serviço para a funcionalidade de visualização de dependência e como Mapa do Serviço não está disponível no Azure governamental no momento, essa funcionalidade não está disponível no Azure governamental.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Instalei o Microsoft Monitoring Agent (MMA) e o Dependency Agent em minhas VMs locais, mas as dependências agora aparecem no portal de migrações para Azure.

Depois de instalar os agentes, as migrações para Azure normalmente leva 15-30 minutos para exibir as dependências no Portal. Se você tiver aguardado por mais de 30 minutos, verifique se o agente do MMA é capaz de se comunicar com o espaço de trabalho do OMS seguindo as etapas abaixo:

Para VM do Windows:
1. Vá para o **painel de controle** e inicie o **Microsoft Monitoring Agent**.
2. Vá para a guia **log Analytics do Azure (OMS)** no pop-up Propriedades de MMA.
3. Verifique se o **status** do espaço de trabalho é verde.
4. Se o status não for verde, tente remover o espaço de trabalho e adicioná-lo novamente ao MMA.
        ![Status do MMA](./media/troubleshooting-general/mma-status.png)

Para a VM do Linux, verifique se os comandos de instalação do MMA e do agente de dependência foram bem-sucedidos.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Quais são os sistemas operacionais com suporte do MMA?

A lista de sistemas operacionais Windows com suporte do MMA está [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
A lista de sistemas operacionais Linux com suporte do MMA está [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Quais são os sistemas operacionais com suporte do agente de dependência?

A lista de sistemas operacionais Windows com suporte pelo agente de dependência está [aqui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
A lista de sistemas operacionais Linux com suporte do Dependency Agent está [aqui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Não consigo visualizar dependências em migrações do Azure por mais de uma duração de hora?
As migrações para Azure permitem que você visualize dependências por até uma duração de até uma hora. Embora a migração do Azure permita que você volte a uma data específica no histórico de até o último mês, a duração máxima para a qual você pode visualizar as dependências é de até 1 hora. Por exemplo, você pode usar a funcionalidade de duração de tempo no mapa de dependências para exibir dependências de ontem, mas só pode exibi-la para uma janela de uma hora. No entanto, você pode usar os logs de Azure Monitor para [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) por uma duração maior.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Não consigo visualizar dependências de grupos com mais de 10 VMs?
Você pode [Visualizar as dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) que têm até 10 VMS, se você tiver um grupo com mais de 10 VMS, recomendamos que você divida o grupo em grupos menores e visualize as dependências.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Instalei os agentes e usei a visualização de dependência para criar grupos. Agora, após o failover, os computadores mostram a ação "instalar agente" em vez de "Exibir dependências"
* Após o failover planejado ou não planejado, as máquinas locais são desativadas e as máquinas equivalentes são giradas no Azure. Esses computadores adquirem um endereço MAC diferente. Eles podem adquirir um endereço IP diferente com base em se o usuário optou por manter o endereço IP local ou não. Se os endereços MAC e IP forem diferentes, as migrações para Azure não associarão os computadores locais a dados de dependência Mapa do Serviço e solicitarão que o usuário instale agentes em vez de exibir dependências.
* Failover pós-teste, os computadores locais permanecem ligados conforme o esperado. As máquinas equivalentes giradas no Azure adquirem um endereço MAC diferente e podem adquirir um endereço IP diferente. A menos que o usuário bloqueie o tráfego de logs de Azure Monitor de saída desses computadores, as migrações para Azure não associam os computadores locais a qualquer Mapa do Serviço dados de dependência e solicitam que o usuário instale agentes em vez de exibir dependências.

## <a name="collect-azure-portal-logs"></a>Coletar logs de portal do Azure

**Como fazer coletar portal do Azure logs de tráfego de rede?**

1. Abra o navegador e navegue e entre no [portal](https://portal.azure.com).
2. Pressione F12 para iniciar a Ferramentas para Desenvolvedores. Se necessário, desmarque a configuração **Limpar entradas na navegação**.
3. Clique na guia **rede** e comece a capturar o tráfego de rede:
   - No Chrome, selecione **preservar log**. A gravação deve ser iniciada automaticamente. Um círculo vermelho indica que o tráfego está sendo capturado. Se ele não aparecer, clique no círculo preto para iniciar.
   - No Microsoft Edge/IE, a gravação deve ser iniciada automaticamente. Se não estiver, clique no botão verde Play.
4. Tente reproduzir o erro.
5. Depois de encontrar o erro durante a gravação, interrompa a gravação e salve uma cópia da atividade gravada:
   - No Chrome, clique com o botão direito do mouse e clique em **salvar como Har com conteúdo**. Isso compacta e exporta os logs como um arquivo. Har.
   - No Microsoft Edge/IE, clique no ícone **Exportar tráfego capturado** . Isso compacta e exporta o log.
6. Navegue até a guia **console** para verificar se há avisos ou erros. Para salvar o log do console:
   - No Chrome, clique com o botão direito do mouse em qualquer lugar no log do console. Selecione **salvar como**, para exportar e compactar o log.
   - No Microsoft Edge/IE, clique com o botão direito do mouse nos erros e selecione **copiar tudo**.
7. Feche Ferramentas para Desenvolvedores.
