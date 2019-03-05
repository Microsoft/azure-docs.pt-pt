---
title: Resolver problemas do Azure Migrate | Documentos da Microsoft
description: Fornece uma descrição geral dos problemas conhecidos no serviço Azure Migrate e dicas para erros comuns de resolução de problemas.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: raynew
ms.openlocfilehash: e85608c411c0aea7b7bf71be19939f6859139c56
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314378"
---
# <a name="troubleshoot-azure-migrate"></a>Resolver problemas do Azure Migrate

## <a name="troubleshoot-common-errors"></a>Resolver erros comuns

[O Azure Migrate](migrate-overview.md) avalia as cargas de trabalho no local para migração para o Azure. Utilize este artigo para resolver os problemas quando implementar e utilizar o Azure Migrate.

### <a name="i-am-using-the-ova-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Estou usando o OVA que Deteta continuamente meu ambiente no local, mas as VMs que são eliminadas no meu ambiente no local ainda estão a ser mostradas no portal.

A aplicação de deteção contínua só recolhe dados de desempenho continuamente, ele não detecta qualquer alteração de configuração no ambiente no local (ou seja, a adição de VM, eliminação, a adição de disco etc.). Se houver uma alteração de configuração no ambiente no local, pode fazer o seguinte para refletir as alterações no portal:

- Adição de itens (VMs, discos, núcleos, etc.): Para refletir estas alterações no portal do Azure, pode parar a deteção a partir da aplicação e, em seguida, inicie-o novamente. Isto irá garantir que as alterações são atualizadas no projeto do Azure Migrate.

   ![Parar deteção](./media/troubleshooting-general/stop-discovery.png)

- Eliminação das VMs: A forma como a aplicação foi concebida, a eliminação de VMs não será refletida, mesmo se parar e iniciar a deteção. Isto acontece porque os dados das deteções subsequentes são anexados às deteções mais antigas e não são substituídos. Neste caso, pode simplesmente ignorar a VM no portal, ao removê-la do seu grupo e recalcular a avaliação.

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Exclusão de projetos do Azure Migrate e espaço de trabalho do Log Analytics associado

Quando elimina um projeto do Azure Migrate, elimina o projeto de migração, juntamente com todos os grupos e avaliações. No entanto, se tiver ligado uma área de trabalho do Log Analytics ao projeto, ele não elimina automaticamente o espaço de trabalho do Log Analytics. Isto acontece porque a mesma área de trabalho do Log Analytics pode ser usada para vários casos de utilização. Se quiser eliminar a área de trabalho do Log Analytics também, terá de fazê-lo manualmente.

1. Navegue para a área de trabalho do Log Analytics ligada ao projeto.
   a. Se ainda não eliminou o projeto de migração, pode encontrar o link para a área de trabalho da página de descrição geral do projeto na secção Essentials.

   ![Área de trabalho LA](./media/troubleshooting-general/LA-workspace.png)

   b. Caso tenha eliminado já o projeto de migração, clique em **grupos de recursos** no painel esquerdo no portal do Azure e vá para o grupo de recursos em que a área de trabalho foi criado e, em seguida, navegar para o mesmo.
2. Siga as instruções [neste artigo](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) para eliminar a área de trabalho.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Falha na criação de projeto de migração com erro *pedidos devem conter cabeçalhos de identidade do utilizador*

Este problema pode ocorrer por utilizadores que não tem acesso ao inquilino do Azure Active Directory (Azure AD) da organização. Quando um utilizador é adicionado a um inquilino do Azure AD pela primeira vez, ele recebe um convite por e-mail para associar o inquilino. Os utilizadores precisam aceder ao e-mail e aceitar o convite seja adicionado com êxito para o inquilino. Se for não é possível ver a mensagem de e-mail, entrar em contacto para um utilizador que já tem acesso ao inquilino e peça-lhe para reenviar o convite ao utilizador através dos passos especificados [aqui](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users).

Quando é recebido o e-mail de convite, terá de abrir o e-mail e clique na ligação no e-mail para aceitar o convite. Depois de o fazer, tem de iniciar sessão fora do portal do Azure e o início de sessão novamente, atualizar o browser não funcionará. Em seguida, pode tentar criar o projeto de migração.

### <a name="i-am-unable-to-export-the-assessment-report"></a>Eu sou não é possível exportar o relatório de avaliação

Se não é possível exportar o relatório de avaliação a partir do portal, tente utilizar a abaixo da API REST para obter um URL de transferência para o relatório de avaliação.

1. Instale *armclient* no seu computador (se não estiver já instalada):

  a. Numa janela de linha de comandos de administrador, execute o seguinte comando: ```@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"```

  b. Numa janela do Windows PowerShell de administrador, execute o seguinte comando: ```choco install armclient```

2.  Obter o URL de transferência para o relatório de avaliação com a API de REST de migrar do Azure

  a.    Numa janela do Windows PowerShell de administrador, execute o seguinte comando: ```armclient login```

  Esta ação abre o Azure início de sessão pop-up em que precisa ao iniciar sessão no Azure.

  b.    Na mesma janela do PowerShell, execute o seguinte comando para obter o URL de transferência para o relatório de avaliação (substitua os parâmetros do URI com os valores apropriados, a API de exemplo do pedido abaixo)

       ```armclient POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Migrate/projects/{projectName}/groups/{groupName}/assessments/{assessmentName}/downloadUrl?api-version=2018-02-02```

       Pedido de exemplo e a saída:

       ```PS C:\WINDOWS\system32> armclient POST https://management.azure.com/subscriptions/8c3c936a-c09b-4de3-830b-3f5f244d72e9/r
esourceGroups/ContosoDemo/providers/Microsoft.Migrate/projects/Demo/groups/contosopayroll/assessments/assessment_11_16_2
018_12_16_21/downloadUrl?api-version=2018-02-02
{
  "assessmentReportUrl": "https://migsvcstoragewcus.blob.core.windows.net/4f7dddac-f33b-4368-8e6a-45afcbd9d4df/contosopayrollassessment_11_16_2018_12_16_21?sv=2016-05-31&sr=b&sig=litQmHuwi88WV%2FR%2BDZX0%2BIttlmPMzfVMS7r7dULK7Oc%3D&st=2018-11-20T16%3A09%3A30Z&se=2018-11-20T16%3A19%3A30Z&sp=r",
  "expirationTime": "2018-11-20T22:09:30.5681954+05:30"```

3. Copie o URL da resposta e abra-o num browser para transferir o relatório de avaliação.

4. Assim que o relatório é transferido, utilize o Excel para navegar para a pasta transferida e abra o ficheiro no Excel para vê-la.

### <a name="performance-data-for-disks-and-networks-adapters-shows-as-zeros"></a>Dados de desempenho para os adaptadores de discos e redes mostram como os zeros

Isto pode ocorrer se o nível de definição de estatística no vCenter server está definido como inferior a três. Em três nível ou superior, o vCenter armazena histórico de desempenho da VM para computação, armazenamento e rede. Para o nível menos de três, vCenter não armazena o armazenamento e dados de rede, mas apenas para dados da CPU e memória. Neste cenário, dados de desempenho mostra como zero no Azure Migrate e do Azure Migrate oferece a recomendação de tamanho para discos e redes com base nos metadados recolhidos das máquinas no local.

Para ativar a recolha de dados de desempenho de disco e rede, altere o nível de definições de estatísticas como três. Em seguida, aguarde, pelo menos, um dia para descobrir o seu ambiente e avaliá-lo.

### <a name="i-specified-an-azure-geography-while-creating-a-migration-project-how-do-i-find-out-the-exact-azure-region-where-the-discovered-metadata-would-be-stored"></a>Eu especifiquei uma geografia do Azure, ao criar um projeto de migração, como posso saber a região do Azure exata em que os metadados detetados seriam armazenados?

Pode ir para o **Essentials** secção a **descrição geral** página do projeto para identificar o local exato onde os metadados são armazenados. A localização é selecionada aleatoriamente na geografia pelo Azure Migrate e não é possível modificá-lo. Se pretender criar um projeto em apenas uma região específica, pode utilizar as APIs REST para criar o projeto de migração e passar para a região desejada.

   ![Localização do projeto](./media/troubleshooting-general/geography-location.png)

## <a name="collector-issues"></a>Problemas de recoletor

### <a name="deployment-of-azure-migrate-collector-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Falha na implementação do Recoletor do Azure Migrate com o erro: O ficheiro de manifesto fornecido é inválido: Entrada do manifesto OVF inválida.

1. Certifique-se de que se a ficheiros do Azure Migrate Recoletor OVA é transferido corretamente ao verificar o seu valor de hash. Consulte o [artigo](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance) para verificar o valor de hash. Se o valor de hash não corresponde, transfira o ficheiro OVA novamente e repetir a implementação.
2. Se ainda assim falhar e se estiver a utilizar o Cliente VMware vSphere para implementar o OVF, tente implementá-lo por meio do Cliente Web vSphere. Se continuar a falhar, tente utilizar outro navegador da web.
3. Se estiver a utilizar o cliente de web do vSphere e tentar implantá-lo no vCenter Server 6.5 ou 6.7, tentar implementar o OVA diretamente no anfitrião ESXi ao seguir os passos abaixo:
  - Ligar ao anfitrião ESXi diretamente (em vez do vCenter Server) com o cliente web (https:// <*endereço IP do anfitrião*>/UI)
  - Aceda à página inicial > inventário
  - Clique em ficheiro > modelo implementar OVF > navegue para o OVA e concluir a implementação
4. Se a implementação ainda falhar, contacte o suporte do Azure Migrate.


### <a name="collector-is-not-able-to-connect-to-the-internet"></a>Recoletor não é possível estabelecer ligação à internet

Isto pode acontecer quando a máquina que está a utilizar está atrás de um proxy. Certifique-se de que fornecer as credenciais de autorização se o proxy precisar de um.
Se estiver a utilizar qualquer proxy de firewall baseado em URL para controlar a conectividade de saída, certifique-se a lista aprovada que estes URLs necessários:

**URL** | **Objetivo**  
--- | ---
*.portal.azure.com | Necessário para verificar a conectividade com o serviço do Azure e validar a sincronização de hora problemas.
*.oneget.org | Necessário transferir o powershell com base vCenter PowerCLI módulo.

**O recoletor não é possível ligar à internet devido a uma falha de validação de certificado**

Isto pode acontecer se estiver a utilizar um proxy de interceção para ligar à Internet e se não tiver importado o certificado de proxy para a VM do recoletor. Pode importar o certificado de proxy a utilizar os passos detalhados [aqui](https://docs.microsoft.com/azure/migrate/concepts-collector).

**O recoletor não é possível ligar ao projeto com o ID de projeto e chave copiei do portal.**

Certifique-se de ter copiado e colado as informações corretas. Para resolver problemas, instale o Microsoft Monitoring Agent (MMA) e verificar se o MMA consegue ligar ao projeto da seguinte forma:

1. Na VM do recoletor, transfira o [MMA](https://go.microsoft.com/fwlink/?LinkId=828603).
2. Para iniciar a instalação, clique duas vezes o ficheiro transferido.
3. Na configuração, sobre o **bem-vindo** página, clique em **próxima**. Na página **Termos de Licenciamento**, clique em **Concordo** para aceitar a licença.
4. Na **pasta de destino**, manter ou modificar a pasta de instalação predefinida > **próxima**.
5. Na **opções de configuração do agente**, selecione **Azure Log Analytics** > **seguinte**.
6. Clique em **adicionar** para adicionar uma nova área de trabalho do Log Analytics. Cole o ID de projeto e a chave que copiou. Clique depois em **Seguinte**.
7. Certifique-se de que o agente pode ligar-se ao projeto. Se não for possível, verifique as definições. Se o agente pode ligar, mas o recoletor não é possível, contacte o suporte.


### <a name="error-802-date-and-time-synchronization-error"></a>Erro 802: Erro de sincronização de data e hora

O relógio do servidor pode ser fora de sincronização com a hora atual em mais de cinco minutos. Altere a hora de relógio no coletor de VM para corresponder a hora atual, da seguinte forma:

1. Abra uma linha de comandos de administrador na VM.
2. Para verificar o fuso horário, execute w32tm /tz.
3. Para sincronizar a hora, execute w32tm /resync.

### <a name="vmware-powercli-installation-failed"></a>Falha na instalação do VMware PowerCLI

Recoletor do Azure Migrate transfere PowerCLI e instala-o em que a aplicação. Falha na instalação do PowerCLI pode ser devido a pontos de extremidade inacessíveis para o repositório do PowerCLI. Para resolver problemas, tente instalar manualmente o PowerCLI no coletor de VM a utilizar o passo seguinte:

1. Abra o Windows PowerShell no modo de administrador
2. Ir para o diretório C:\ProgramFiles\ProfilerService\VMWare\Scripts\
3. Execute o script InstallPowerCLI.ps1

### <a name="error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception"></a>Ocorreu um erro interno UnhandledException: System.IO.FileNotFoundException

Este problema pode ocorrer devido a um problema com a instalação do VMware PowerCLI. Siga os passos abaixo para resolver o problema:

1. Se não estiver conectado a versão mais recente da aplicação de recoletor [atualizar o Recoletor para a versão mais recente](https://aka.ms/migrate/col/checkforupdates) e verifique se o problema está resolvido.
2. Se já tiver a versão mais recente do recoletor, siga os passos abaixo para fazer uma instalação limpa do PowerCLI:

   a. Feche o navegador da web na aplicação.

   b. Pare o serviço de "Recoletor do Azure Migrate" indo para o Windows Service Manager (Open 'Executar' e o tipo de Services. msc para abrir o Gestor de serviço do Windows). Clique com o botão direito do rato no serviço do Azure Migrate Recoletor e clique em parar.

   c. Elimine todas as pastas a partir do VMware das seguintes localizações: C:\Program Files\WindowsPowerShell\Modules  
        C:\Program ficheiros (x86) \WindowsPowerShell\Modules

   d. Reinicie o serviço de "Recoletor do Azure Migrate" no Windows Service Manager (Open 'Executar' e o tipo de Services. msc para abrir o Gestor de serviço do Windows). Clique com o botão direito do rato no serviço do Azure Migrate Recoletor e clique em Iniciar.

   e. Faça duplo clique em 'Executar recoletor' do atalho de desktop para iniciar a aplicação recoletor. A aplicação de recoletor deve baixar e instalar a versão necessária do PowerCLI automaticamente.

3. Se o procedimento acima não resolverem o problema, siga os passos um c acima e, em seguida, instale manualmente o PowerCLI na aplicação através dos seguintes passos:

   a. Limpeza do wsu PowerCLI incompleta todos os ficheiros de instalação, seguindo os passos #a para #c no passo 2 de # acima.

   b. Vá para Iniciar > executar > Open PowerShell(x86) do Windows no modo de administrador

   c. Execute o comando:  Install-Module "VMWare.VimAutomation.Core" - RequiredVersion "6.5.2.6234650" (tipo de "A" quando ele solicitar a confirmação)

   d. Reinicie o serviço de "Recoletor do Azure Migrate" no Windows Service Manager (Open 'Executar' e o tipo de Services. msc para abrir o Gestor de serviço do Windows). Clique com o botão direito do rato no serviço do Azure Migrate Recoletor e clique em Iniciar.

   e. Faça duplo clique em 'Executar recoletor' do atalho de desktop para iniciar a aplicação recoletor. A aplicação de recoletor deve baixar e instalar a versão necessária do PowerCLI automaticamente.

4. Se não é possível transferir o módulo na aplicação devido a problemas de firewall, transfira e instale o módulo numa máquina que tem acesso à internet com os seguintes passos:

    a. Limpeza do wsu PowerCLI incompleta todos os ficheiros de instalação, seguindo os passos #a para #c no passo 2 de # acima.

    b. Vá para Iniciar > executar > Open PowerShell(x86) do Windows no modo de administrador

    c. Execute o comando:  Install-Module "VMWare.VimAutomation.Core" - RequiredVersion "6.5.2.6234650" (tipo de "A" quando ele solicitar a confirmação)

    d. Copie todos os módulos começando com "VMware" de "C:\Program Files (x86) \WindowsPowerShell\Modules" para o mesmo local na VM do recoletor.

    e. Reinicie o serviço de "Recoletor do Azure Migrate" no Windows Service Manager (Open 'Executar' e o tipo de Services. msc para abrir o Gestor de serviço do Windows). Clique com o botão direito do rato no serviço do Azure Migrate Recoletor e clique em Iniciar.

    f. Faça duplo clique em 'Executar recoletor' do atalho de desktop para iniciar a aplicação recoletor. A aplicação de recoletor deve baixar e instalar a versão necessária do PowerCLI automaticamente.

### <a name="error-unabletoconnecttoserver"></a>Error UnableToConnectToServer

Não é possível ligar ao vCenter Server "Servername.com:9443" devido ao erro: Não havia um ponto final preparado em https://Servername.com:9443/sdk que pudesse aceitar a mensagem.

Verifique se estiver a executar a versão mais recente da aplicação recoletora, caso contrário, atualizar a aplicação para o [versão mais recente](https://docs.microsoft.com/azure/migrate/concepts-collector).

Se o problema ainda acontece na versão mais recente, é possível porque a máquina do recoletor não consegue resolver o nome do servidor vCenter especificado ou a porta especificada está errada. Por predefinição, que, se a porta não for especificada, o recoletor irá tentar ligar para o número da porta 443.

1. Tente fazer ping ao Servername.com a partir da máquina do recoletor.
2. Se o passo 1 falhar, tente ligar ao servidor do vCenter através do endereço IP.
3. Identifique o número de porta correto para ligar ao vCenter.
4. Por fim, verifique se o servidor do vCenter está em execução.

### <a name="antivirus-exclusions"></a>Exclusões do antivírus

Para proteger a aplicação do Azure Migrate, tem de impedir que as seguintes pastas na aplicação da verificação antivírus:

- Pasta que tenha os binários do serviço do Azure Migrate. Exclua todas as subpastas.
  %ProgramFiles%\ProfilerService  
- Azure Migrate a aplicação Web. Exclua todas as subpastas.
  %SystemDrive%\inetpub\wwwroot
- Cache local para a base de dados e arquivos de log. Do Azure migrate precisa de serviço de acesso RW para esta pasta.
  %SystemDrive%\Profiler

## <a name="dependency-visualization-issues"></a>Problemas de visualização de dependência

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Eu não consigo encontrar a funcionalidade de visualização de dependência para projetos do Azure Government.

O Azure Migrate depende do mapa de serviço para a funcionalidade de visualização de dependência e uma vez que o mapa de serviço não está atualmente disponível no Azure Government, esta funcionalidade não está disponível no Azure Government.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Instalei o Microsoft Monitoring Agent (MMA) e o agente de dependência em minhas VMs no local, mas as dependências são agora aparecer no portal do Azure Migrate.

Depois de instalar os agentes, do Azure Migrate, normalmente, demora 15 a 30 minutos para apresentar as dependências no portal. Se se esperou durante mais de 30 minutos, certifique-se de que o agente MMA é capaz de comunicar com a área de trabalho do OMS ao seguir os passos abaixo:

Para a Windows VM:
1. Aceda a **painel de controlo** e inicie **Microsoft Monitoring Agent**
2. Vá para o **do Azure Log Analytics (OMS)** separador nas propriedades do MMA pop-up
3. Certifique-se de que o **estado** para a área de trabalho é verde.
4. Se o estado não for verde, tente remover a área de trabalho e adicionando-a novamente para o MMA.
        ![Estado do MMA](./media/troubleshooting-general/mma-status.png)

Para VM do Linux, certifique-se de que os comandos de instalação para o agente MMA e de dependência tinham sido concluída.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Quais são os sistemas operativos suportados por MMA?

É a lista de sistemas de operativos do Windows suportados pelo MMA [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
É a lista de sistemas de operativos Linux suportados pelo MMA [aqui](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Quais são os sistemas operativos suportados pelo agente de dependência?

A lista de sistemas de operativos do Windows suportados pelo agente de dependência é [aqui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
A lista dos sistemas de operativos Linux suportados pelo agente de dependência é [aqui](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Não consigo me visualize as dependências no Azure Migrate para obter mais informações à duração de uma hora?
O Azure Migrate permite que visualize as dependências de duração do até uma hora. Embora o Azure Migrate permite-lhe voltar atrás para uma data específica na história para até o último mês, a duração máxima para o qual é possível visualizar as dependências é até uma hora. Por exemplo, pode usar a funcionalidade de duração de tempo do mapa de dependência, para ver as dependências de ontem, mas pode apenas visualizá-lo para uma janela de uma hora. No entanto, pode utilizar os registos do Azure Monitor para [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#query-dependency-data-from-log-analytics) ao longo de um período mais longo.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Não consigo me visualize as dependências para grupos com mais de 10 VMs?
Pode [visualize as dependências para grupos](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) que têm cópia de segurança para 10 VMs, se tiver um grupo com mais de 10 VMs, recomendamos que dividir o grupo em grupos mais pequenos e visualize as dependências.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Eu agentes instalados e usado a visualização de dependência para criar grupos. Agora após a ativação pós-falha, as máquinas mostram a ação de "Instalar o agente", em vez de "Dependências do modo de exibição"
* Post planeada ou ativação pós-falha não planeada, no local máquinas estão desativadas e máquinas equivalentes são criadas no Azure. Estas máquinas adquirir um endereço MAC diferente. Eles podem adquirir um endereço IP diferente, com base em se o utilizador optou por manter o endereço IP no local ou não. Se forem diferentes endereços MAC e IP, o Azure Migrate não associa as máquinas no local com quaisquer dados de dependência de mapa de serviço e pede ao utilizador para instalar agentes em vez de visualização de dependências.
* Após a ativação pós-falha de teste, as máquinas no local mantêm ativadas conforme esperado. As máquinas equivalentes rotacionadas no Azure adquirir o endereço MAC diferente e podem adquirir o endereço IP diferente. A menos que os blocos de utilizador saída do Azure Monitor faz o tráfego destas máquinas, do Azure Migrate não associa as máquinas no local com quaisquer dados de dependência de mapa de serviço e pede ao utilizador para instalar agentes em vez de visualização de dependências.

## <a name="troubleshoot-azure-readiness-issues"></a>Resolver problemas de preparação para o Azure

**Problema** | **Fix**
--- | ---
Tipo de arranque não suportado | Azure não suporta VMs com o tipo de arranque EFI. Recomenda-se para converter o tipo de arranque BIOS antes de executar uma migração. <br/><br/>Pode usar [do Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) para fazer a migração dessas VMs, pois ele converterá o tipo de arranque da VM para BIOS durante a migração.
SO de Windows condicionalmente suportado | O sistema operacional passou a sua data de fim de suporte e precisa de um personalizado suporta contrato (CSA) para [suportar no Azure](https://aka.ms/WSosstatement), considere atualizar o SO antes de migrar para o Azure.
SO Windows não suportado | O Azure suporta apenas [selecionadas de versões de SO Windows](https://aka.ms/WSosstatement), considere atualizar o SO da máquina antes de migrar para o Azure.
SO Linux condicionalmente endossado | Azure endossa apenas [selecionadas de versões de SO Linux](../virtual-machines/linux/endorsed-distros.md), considere atualizar o SO da máquina antes de migrar para o Azure.
SO Linux não endossado | A máquina pode arrancar no Azure, mas não existe suporte de SO é fornecida pelo Azure, considere atualizar o sistema operativo para uma [endorsed Linux versão](../virtual-machines/linux/endorsed-distros.md) antes de migrar para o Azure
Sistema de operativo desconhecido | O sistema operativo da VM foi especificado como 'Outro' no vCenter Server, devido a que o Azure Migrate não é possível identificar a preparação para o Azure da VM. Certifique-se de que é o sistema operacional em execução no interior da máquina [suportado](https://aka.ms/azureoslist) pelo Azure antes de migrar a máquina.
Número de bits do SO não suportados | VMs com o sistema operacional de 32 bits podem arrancar no Azure, mas recomenda-se para atualizar o SO da VM de 32 bits para 64 bits antes de migrar para o Azure.
Requer subscrição do Visual Studio. | A máquina tem um sistema operacional em execução que de cliente do Windows é suportado apenas na subscrição do Visual Studio.
VM não encontrada para o desempenho de armazenamento necessária. | O desempenho de armazenamento (IOPS/débito) necessário para a máquina excede o suporte de VM do Azure. Reduza os requisitos de armazenamento para a máquina antes da migração.
Não foi encontrada para o desempenho de rede necessária uma VM. | O desempenho de rede (entrada/saída) necessário para a máquina excede o suporte de VM do Azure. Reduza os requisitos de rede para a máquina.
VM não foi encontrada na localização especificada. | Utilize uma localização de destino diferente antes da migração.
Um ou mais discos não são adequados. | Um ou mais discos ligados à VM não satisfaz os requisitos do Azure. Para cada disco anexado à VM, certifique-se de que o tamanho do disco é < 4 TB; caso contrário, reduzir o tamanho de disco antes de migrar para o Azure. Certifique-se de que o desempenho (IOPS/débito) necessário para cada disco é suportado pelo Azure [discos da máquina virtual geridos](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits).   
Um ou mais adaptadores de rede não são adequados. | Remova os adaptadores de rede não utilizadas da máquina antes da migração.
A contagem do disco excede o limite | Remova os discos não utilizados do computador antes da migração.
O tamanho do disco excede o limite | O Azure suporta discos com tamanho 4 TB. Reduzir discos para menos de 4 TB antes da migração.
Disco indisponível na localização especificada | Certifique-se de que o disco está na localização do seu destino antes de migrar.
Disco indisponível para a redundância especificada | O disco deve utilizar o tipo de redundância de armazenamento definido nas definições de avaliação (LRS, por predefinição).
Não foi possível determinar a adequação do disco devido a um erro interno | Tente criar uma nova avaliação para o grupo.
Não foi encontrada uma VM com os núcleos e memória necessários | Azure não foi bem um tipo VM adequado. Reduza a memória e o número de núcleos da máquina no local antes de migrar.
Não foi possível determinar a adequação da VM devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação de um ou mais discos devido a um erro interno. | Tente criar uma nova avaliação para o grupo.
Não foi possível determinar a adequação de um ou mais adaptadores de rede devido a um erro interno. | Tente criar uma nova avaliação para o grupo.


## <a name="collect-logs"></a>Recolher registos

**Como recolher registos a na VM do recoletor?**

Registo está ativado por predefinição. Os registos estão localizados da seguinte forma:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Para recolher eventos de rastreio para Windows, faça o seguinte:

1. Na VM do recoletor, abra uma janela de comando do PowerShell.
2. Execute **Get-EventLog - LogName aplicação | export-csv eventlog.csv**.

**Como posso recolher registos de tráfego de rede portal?**

1. Abra o browser e navegue e inicie sessão [para o portal](https://portal.azure.com).
2. Premir a tecla F12 para iniciar as ferramentas de desenvolvimento. Se for necessário, desmarque a definição **limpar as entradas na navegação**.
3. Clique nas **rede** separador e comece a capturar o tráfego de rede:
 - No Chrome, selecione **Preserve log**. A gravação deve iniciar automaticamente. Um círculo vermelho indica que o tráfego está sendo captura. Se não aparecer, clique no círculo preto para começar
 - No Microsoft Edge/IE, gravação deve iniciar automaticamente. Se não, clique no botão verde play.
4. Tente reproduzir o erro.
5. Depois de já se deparou com o erro durante a gravação, interromper a gravação e salvar uma cópia da atividade registada:
 - No Chrome, com o botão direito e clique em **guardar como HAR com conteúdo**. Isso zips e exporta os registos como um ficheiro de .har.
 - No Microsoft Edge/IE, clique nas **exportação capturado o tráfego** ícone. Isso zips e exporta o log.
6. Navegue para o **consola** separador para verificar a existência de quaisquer avisos ou erros. Para guardar o registo da consola:
 - No Chrome, com o botão direito em qualquer lugar no registo de consola. Selecione **guardar como**, para exportar e zip no registo.
 - No Microsoft Edge/IE, com o botão direito sobre os erros e selecione **copie todos os**.
7. Feche as ferramentas de programação.

## <a name="collector-error-codes-and-recommended-actions"></a>Códigos de erro do recoletor e ações recomendadas

| Código de Erro | Nome do erro   | Mensagem   | Causas possíveis | Ação recomendada  |
| --- | --- | --- | --- | --- |
| 601       | CollectorExpired               | O recoletor expirou.                                                        | Recoletor Expirado.                                                                                    | Transfira uma nova versão do recoletor e tente novamente.                                                                                      |
| 751       | UnableToConnectToServer        | Não foi possível ligar ao vCenter Server "%Name;" devido ao erro: %ErrorMessage;     | Consulte a mensagem de erro para obter detalhes.                                                             | Resolva o problema e tente novamente.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | O servidor "%Name;" não é um vCenter Server.                                  | Forneça os detalhes do vCenter Server.                                                                       | Tente novamente a operação com os detalhes do vCenter Server corretos.                                                                                   |
| 753       | InvalidLoginCredentials        | Não é possível ligar ao vCenter Server "%Name;" devido ao erro: %ErrorMessage; | Falha ao ligar ao vCenter Server devido a credenciais de início de sessão inválidas.                             | Certifique-se de que as credenciais de início de sessão fornecidas estão corretas.                                                                                    |
| 754       | NoPerfDataAvailable           | Dados de desempenho não disponíveis.                                               | Verifique o nível de estatísticas no vCenter Server. Ele deve ser definido como 3 para dados de desempenho estejam disponíveis. | Alterar o Nível de Estatísticas para 3 (para duração de 5 minutos, 30 minutos e 2 horas) e experimente aguardar pelo menos um dia.                   |
| 756       | NullInstanceUUID               | Foi encontrada uma máquina com InstanceUUID nulo                                  | O vCenter Server pode ter um objeto inapropriado.                                                      | Resolva o problema e tente novamente.                                                                                                           |
| 757       | VMNotFound                     | A máquina virtual não foi encontrada                                                  | A máquina virtual pode ser eliminada: %VMID;                                                                | Certifique-se de que as máquinas virtuais selecionadas enquanto efetua o controlo do âmbito do inventário do vCenter existem durante a deteção                                      |
| 758       | GetPerfDataTimeout             | O pedido do VCenter excedeu o tempo limite. Mensagem % Message;                                  | As credenciais do vCenter Server estão incorretas                                                              | Verifique as credenciais do vCenter Server e certifique-se de que o vCenter Server está acessível. Repita a operação. Se o problema persistir, contacte o suporte. |
| 759       | VmwareDllNotFound              | DLL VMWare.Vim não encontrado.                                                     | O PowerCLI não se encontra corretamente instalado.                                                                   | Verifique se o PowerCLI está corretamente instalado. Repita a operação. Se o problema persistir, contacte o suporte.                               |
| 800       | ServiceError                   | O serviço do Recoletor do Azure Migrate não está em execução.                               | O serviço do Recoletor do Azure Migrate não está em execução.                                                       | Utilize o services.msc para iniciar o serviço e tentar novamente a operação.                                                                             |
| 801       | PowerCLIError                  | A instalação do VMware PowerCLI falhou.                                          | A instalação do VMware PowerCLI falhou.                                                                  | Repita a operação. Se o problema persistir, instale-o manualmente e repita a operação.                                                   |
| 802       | TimeSyncError                  | A hora não está sincronizada com o servidor de horas da internet.                            | A hora não está sincronizada com o servidor de horas da internet.                                                    | Certifique-se de que a hora no computador está bem definida para o fuso horário do computador e repita a operação.                                 |
| 702       | OMSInvalidProjectKey           | Chave de projeto inválida especificada.                                                | Chave de projeto inválida especificada.                                                                        | Repita a operação com a chave de projeto correta.                                                                                              |
| 703       | OMSHttpRequestException        | Erro ao enviar o pedido. Mensagem % Message;                                | Verifique o ID e a chave do projeto e certifique-se de que ponto final está alcançável.                                       | Repita a operação. Se o problema persistir, contacte o Suporte da Microsoft.                                                                     |
| 704       | OMSHttpRequestTimeoutException | Pedido HTTP excedeu o tempo limite. Mensagem % Message;                                     | Verifique o id e a chave do projeto e certifique-se de que ponto final está alcançável.                                        | Repita a operação. Se o problema persistir, contacte o Suporte da Microsoft.                                                                     |
