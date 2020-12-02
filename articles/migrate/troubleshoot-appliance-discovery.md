---
title: Resolução de problemas Azure Migrar implantação e descoberta de aparelhos
description: Obtenha ajuda na implantação do aparelho Azure Migrate e na descoberta de máquinas.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 0d4630cad94a30e0dd53785c512e452d7b4a7d38
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494448"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Resolução de problemas do aparelho Esmigósia Azure Migrar e descoberta

Este artigo ajuda-o a resolver problemas ao colocar o aparelho [Azure Migrate](migrate-services-overview.md) e a utilizar o aparelho para descobrir máquinas no local.


## <a name="whats-supported"></a>O que é suportado?

[Reveja](migrate-appliance.md) os requisitos de suporte do aparelho.


## <a name="invalid-ovf-manifest-entry"></a>"Entrada manifesto OVF inválida"

Se receber o erro "O ficheiro manifesto fornecido é inválido: Entrada manifesto OVF inválida", faça o seguinte:

1. Verifique se o ficheiro OVA do aparelho Azure Migrate é descarregado corretamente, verificando o seu valor de haxixe. [Saiba mais](./tutorial-discover-vmware.md). Se o valor do haxixe não corresponder, descarregue novamente o ficheiro OVA e volte a tentar a implementação.
2. Se a implementação ainda falhar, e estiver a utilizar o cliente VMware vSphere para implementar o ficheiro OVF, tente implantá-lo através do cliente web vSphere. Se a implementação ainda falhar, tente utilizar um navegador web diferente.
3. Se estiver a utilizar o cliente web vSphere e a tentar implantá-lo no vCenter Server 6.5 ou 6.7, tente implantar o OVA diretamente no anfitrião ESXi:
   - Ligue-se diretamente ao anfitrião ESXi (em vez do vCenter Server) com o cliente web (https://<*anfitrião endereço IP*>/ui).
   - No **Inventário Doméstico**,  >  **Inventory** selecione o modelo de OVF de **File**  >  **implementação de ficheiros**. Navegue pelo OVA e complete a colocação.
4. Se a implantação ainda falhar, contacte o suporte da Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Não se liga à internet

Isto pode acontecer se a máquina do aparelho estiver por detrás de um representante.

- Confirme que introduz as credenciais de autorização, se o proxy assim o exigir.
- Se estiver a usar um representante de firewall baseado em URL para controlar a conectividade de saída, adicione [estes URLs](migrate-appliance.md#url-access) a uma lista de indicações.
- Se estiver a utilizar um representante de interceção para ligar à internet, importe o certificado de procuração para o VM do aparelho utilizando [estes passos](./migrate-appliance.md).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Não pode assinar no Azure a partir da aplicação web do aparelho

O erro "Desculpe, mas estamos com dificuldades em contratar-te" aparece se estiveres a usar a conta Azure incorreta para assinar no Azure. Este erro ocorre por um par de razões:

- Se você assinar na aplicação web do aparelho para a nuvem pública, usando credenciais de conta de utilizador para o portal de nuvem do Governo.
- Se você assinar na aplicação web do aparelho para a nuvem do governo usando credenciais de conta de utilizador para o portal de nuvem privada.

Certifique-se de que está a usar as credenciais corretas.

##  <a name="datetime-synchronization-error"></a>Erro de sincronização de data/hora

Um erro sobre a sincronização da data e da hora (802) indica que o relógio do servidor pode estar fora de sincronização com o tempo atual em mais de cinco minutos. Altere o tempo do relógio no VM do coletor para corresponder à hora atual:

1. Abra um pedido de comando administrativo no VM.
2. Para verificar o fuso horário, corra **w32tm /tz**.
3. Para sincronizar o tempo, corra **w32tm /resync**.


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Se obter este erro de ligação, poderá não conseguir ligar-se ao *nome do servidor do* servidor do vCenter .com:9443. Os detalhes do erro indicam que não há nenhum ponto final a ouvir `https://\*servername*.com:9443/sdk` que possa aceitar a mensagem.

- Verifique se está a executar a versão mais recente do aparelho. Se não estiver, atualize o aparelho para a [versão mais recente](./migrate-appliance.md).
- Se o problema ainda ocorrer na versão mais recente, o aparelho poderá não conseguir resolver o nome do servidor vCenter especificado, ou a porta especificada pode estar errada. Por predefinição, se a porta não for especificada, o coletor tentará ligar-se ao número da porta 443.

    1. O *ping servername*.com do aparelho.
    2. Se o passo 1 falhar, tente ligar-se ao servidor vCenter utilizando o endereço IP.
    3. Identifique o número de porta correto para ligar ao servidor vCenter.
    4. Verifique se o vCenter Server está a funcionar.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Erro 60052/60039: O aparelho pode não estar registado

- Erro 60052: "O aparelho pode não ser registado com sucesso no projeto Azure Migrate" ocorrer se a conta Azure utilizada para registar o aparelho não tiver permissões suficientes.
    - Certifique-se de que a conta de utilizador Azure utilizada para registar o aparelho tem pelo menos permissões do Contribuinte na subscrição.
    - [Saiba mais](./migrate-appliance.md#appliance---vmware) sobre as funções e permissões necessárias do Azure.
- Erro 60039: "O aparelho pode não ser registado com sucesso no projeto Azure Migrate" pode ocorrer se o registo falhar porque o projeto Azure Migrate usado para o registo do aparelho não pode ser encontrado.
    - No portal Azure e verifique se o projeto existe no grupo de recursos.
    - Se o projeto não existir, crie um novo projeto Azure Migrate no seu grupo de recursos e registe novamente o aparelho. [Saiba como](./create-manage-projects.md#create-a-project-for-the-first-time) criar um novo projeto.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Erro 60030/60031: A operação de gestão do Cofre falhou

Se receber o erro 60030 ou 60031, "Uma operação de gestão do Cofre da Chave Azure falhou", faça o seguinte:
- Certifique-se de que a conta de utilizador Azure utilizada para registar o aparelho tem pelo menos permissões do Contribuinte na subscrição.
- Certifique-se de que a conta tem acesso ao cofre de chaves especificado na mensagem de erro e, em seguida, re-tentar a operação.
- Se o problema persistir, contacte o suporte da Microsoft.
- [Saiba mais](./migrate-appliance.md#appliance---vmware) sobre as funções e permissões necessárias do Azure.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Erro 60028: A descoberta não pôde ser iniciada

Erro 60028: "A descoberta não pôde ser iniciada por causa de um erro. A operação falhou na lista especificada de anfitriões ou clusters" indica que a descoberta não pôde ser iniciada nos anfitriões listados no erro devido a um problema no acesso ou recuperação de informações em VM. Os restantes anfitriões foram adicionados com sucesso.

- Adicione novamente os anfitriões listados no erro, utilizando a opção **adicionar.**
- Se houver um erro de validação, reveja a orientação de remediação para corrigir os erros e, em seguida, tente a opção **Salvar e iniciar novamente** a opção de descoberta.

## <a name="error-60025-azure-ad-operation-failed"></a>Erro 60025: A operação AD Azure falhou 
Erro 60025: "Uma operação AD Azure falhou. O erro ocorreu ao criar ou atualizar a aplicação AZure AD" ocorre quando a conta de utilizador Azure utilizada para iniciar a descoberta é diferente da conta utilizada para registar o aparelho. Faça um dos seguintes:

- Certifique-se de que a conta de utilizador que inicia a descoberta é a mesma que foi utilizada para registar o aparelho.
- Fornecer permissões de acesso à aplicação Azure Ative para a conta do utilizador para as quais a operação de descoberta está a falhar.
- Elimine o grupo de recursos anteriormente criado para o projeto Azure Migrate. Criar outro grupo de recursos para recomeçar.
- [Saiba mais](./migrate-appliance.md#appliance---vmware) sobre as permissões de candidatura do Azure Ative Directory.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Erro 50004: Não se pode ligar ao hospedeiro ou ao cluster

Erro 50004: "Não é possível ligar-se a um hospedeiro ou cluster porque o nome do servidor não pode ser resolvido. O código de erro winRM: 0x803381B9" pode ocorrer se o serviço Azure DNS para o aparelho não conseguir resolver o cluster ou o nome de anfitrião que forneceu.

- Se vir este erro no cluster, cluster FQDN.
- Você também pode ver este erro para os anfitriões em um cluster. Isto indica que o aparelho pode ligar-se ao cluster, mas o cluster devolve nomes de anfitriões que não são FQDNs. Para resolver este erro, atualize o ficheiro dos anfitriões no aparelho adicionando um mapeamento do endereço IP e dos nomes dos anfitriões:
    1. Abra o Bloco de Notas como administrador.
    2. Abra o ficheiro C:\Windows\System32\Drivers\etc\hosts.
    3. Adicione o endereço IP e o nome de anfitrião em seguida. Repita para cada hospedeiro ou cluster onde vê este erro.
    4. Guarde e feche o ficheiro hosts.
    5. Verifique se o aparelho pode ligar-se aos anfitriões, utilizando a aplicação de gestão do aparelho. Após 30 minutos, deverá ver as últimas informações destes anfitriões no portal Azure.


## <a name="error-60001-unable-to-connect-to-server"></a>Erro 60001: Não é possível ligar-se ao servidor 

- Certifique-se de que existe conectividade do aparelho para o servidor
- Se for um servidor linux, certifique-se de que a autenticação baseada em palavras-passe está ativada utilizando os seguintes passos:
    1. Inicie sessão na máquina linux e abra o ficheiro de configuração ssh utilizando o comando 'vi /etc/ssh/sshd_config'
    2. Desfaça a opção "PasswordAuthentication" para sim. Guarde o ficheiro.
    3. Reiniciar o serviço ssh executando "serviço sshd restart"
- Se for um servidor de janelas, certifique-se de que a porta 5985 está aberta para permitir chamadas remotas de WMI.
- Se estiver a descobrir um servidor GCP linux e a utilizar um utilizador de raiz, utilize os seguintes comandos para alterar a definição padrão para o início de sessão de raiz
    1. Inicie sessão na máquina linux e abra o ficheiro de configuração ssh utilizando o comando 'vi /etc/ssh/sshd_config'
    2. Defina a opção "PermitRootLogin" para sim.
    3. Reiniciar o serviço ssh executando "serviço sshd restart"

## <a name="error-no-suitable-authentication-method-found"></a>Erro: Não foi encontrado nenhum método de autenticação adequado

Certifique-se de que a autenticação baseada em palavras-passe está ativada no servidor linux utilizando os seguintes passos:
    1. Inicie sessão na máquina linux e abra o ficheiro de configuração ssh utilizando o comando 'vi /etc/ssh/sshd_config'
    2. Desfaça a opção "PasswordAuthentication" para sim. Guarde o ficheiro.
    3. Reiniciar o serviço ssh executando "serviço sshd restart"


## <a name="discovered-vms-not-in-portal"></a>VMs descobertos não no portal

Se o estado de descoberta é "Discovery in progress", mas ainda não ver os VMs no portal, espere alguns minutos:
- Demora cerca de 15 minutos para um VMware VM.
- Leva cerca de dois minutos para cada hospedeiro adicionado para a descoberta do Hyper-V.

Se esperar e o estado não mudar, **selecione Refresh** no **separador Servidores.** Isto deve mostrar a contagem dos servidores descobertos em Azure Migrate: Server Assessment e Azure Migrate: Server Migration.

Se isto não funcionar e estiver a descobrir servidores VMware:

- Verifique se a conta vCenter especificada tem permissões corretamente definidas, com acesso a pelo menos um VM.
- O Azure Migrate não consegue descobrir VMware VMs se a conta vCenter tiver acesso concedido ao nível da pasta VM do vCenter. [Saiba mais](set-discovery-scope.md) sobre a descoberta de scoping.

## <a name="vm-data-not-in-portal"></a>Dados VM não no portal

Se os VM descobertos não aparecerem no portal ou se os dados de VM estiverem desatualizados, aguarde alguns minutos. Leva até 30 minutos para que as alterações nos dados de configuração VM descobertos apareçam no portal. Pode levar algumas horas para que apareçam alterações nos dados da aplicação. Se não houver dados depois deste tempo, tente refrescar, da seguinte forma.

1. Na Avaliação **do**  >  **Servidor Azure Migrate,** selecione **Overview**.
2. Under **Manage**, selecione **Agent Health**.
3. Selecione **Refresh agent**.
4. Aguarde que a operação de atualização esteja concluída. Deve agora ver informações atualizadas.

## <a name="deleted-vms-appear-in-portal"></a>VMs eliminados aparecem no portal

Se apagar VMs e ainda aparecerem no portal, aguarde 30 minutos. Se ainda aparecerem, refresque-se como descrito acima.

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Não vejo dados de desempenho para alguns adaptadores de rede nos meus servidores físicos

Isto pode acontecer se o servidor físico tiver virtualização Hiper-V ativada. Devido a uma lacuna no produto, a produção da rede é capturada nos adaptadores de rede virtuais descobertos.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Erro: O ficheiro carregado não está no formato esperado
Algumas ferramentas têm definições regionais que criam o ficheiro CSV com o ponto e vírgula como delimitador. Altere as definições para garantir que o delimitador é uma vírgula.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Importei um CSV, mas vejo “Deteção em curso”
Este estado aparece se o seu upload CSV falhou devido a uma falha de validação. Experimente importar o CSV novamente. Pode transferir o relatório de erros do carregamento anterior e seguir a orientação de remediação no ficheiro para corrigir os erros. Pode transferir o relatório de erros a partir da secção “Detalhes da Importação” na página “Detetar computadores”.

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>Não ver detalhes da aplicação mesmo depois de atualizar credenciais de hóspedes
A descoberta da aplicação é uma vez a cada 24 horas. Se quiser ver os detalhes imediatamente, refresque-se da seguinte forma. Isto pode levar alguns minutos dependendo do não. de VMs descobertos.

1. Na Avaliação **do**  >  **Servidor Azure Migrate,** selecione **Overview**.
2. Under **Manage**, selecione **Agent Health**.
3. Selecione **Refresh agent**.
4. Aguarde que a operação de atualização esteja concluída. Deve agora ver informações atualizadas.

## <a name="unable-to-export-application-inventory"></a>Inventário de pedidos de exportação incapaz de exportar
Certifique-se de que o utilizador que descarrega o inventário do portal tem privilégios de Contribuinte na subscrição.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Não se encontra um método de autenticação adequado para a autenticação completa (publickey)
A autenticação baseada em chaves não funcionará, utilize a autenticação de senha.

## <a name="common-app-discovery-errors"></a>Erros comuns de descoberta de aplicativos

A Azure Migrate suporta a descoberta de aplicações, funções e funcionalidades, utilizando Azure Migrate: Server Assessment. A descoberta de aplicações é atualmente suportada apenas para VMware. [Saiba mais](how-to-discover-applications.md) sobre os requisitos e passos para a criação de apps.

Erros típicos de descoberta de aplicações são resumidos na tabela. 

**Erro** | **Motivo** | **Ação**
--- | --- | ---
9000: O estado da ferramenta VMware não pode ser detetado.     |   As ferramentas VMWare podem não ser instaladas ou corrompidas.    |   Certifique-se de que as ferramentas VMware são instaladas e em funcionamento no VM.
9001: As ferramentas VMware não estão instaladas.     |   As ferramentas VMWare podem não ser instaladas ou corrompidas.    |   Certifique-se de que as ferramentas VMware são instaladas e em funcionamento no VM.
9002: As ferramentas VMware não estão a funcionar.   |   As ferramentas VMWare podem não ser instaladas ou corrompidas.    |   Certifique-se de que as ferramentas VMware são instaladas e em funcionamento no VM.
9003: Tipo de sistema operativo não suportado para a descoberta de VM convidado.    |   O sistema operativo que funciona no servidor não é nem Windows nem Linux.    |   Os tipos de sistema operativo suportado são apenas Windows e Linux. Se o servidor for, de facto, Windows ou Linux, verifique o tipo de sistema operativo especificado no vCenter Server.
9004: A VM não está a funcionar.     |   O VM está desligado.  |   Certifique-se de que o VM está ligado.
9005: Tipo de sistema operativo não suportado para a descoberta de VM convidado.    |   Tipo de sistema operativo não suportado para a descoberta de VM do hóspede.     |   Os tipos de sistema operativo suportado são apenas Windows e Linux.
9006: O URL para descarregar o ficheiro de metadados do hóspede está vazio.     |   Isto pode acontecer se o agente de descoberta não estiver a funcionar como esperado.    |   O problema deve resolver-se automaticamente em 24 horas. Se o problema persistir, contacte o Microsoft Support.
9007: O processo de execução da tarefa de descoberta no VM convidado não é encontrado.   |   Isto pode acontecer se o agente de descoberta não estiver a funcionar corretamente.   |   O problema deve resolver-se automaticamente em 24 horas. Se o problema persistir, contacte o Microsoft Support.
9008: O estado do processo VM do hóspede não pode ser recuperado.   |   O problema pode ocorrer devido a um erro interno.   |   O problema deve resolver-se automaticamente em 24 horas. Se o problema persistir, contacte o Microsoft Support.
9009: O Windows UAC impediu a execução de tarefas de descoberta no servidor.  |   As definições de Controlo de Conta de Utilizador do Windows (UAC) no servidor são restritivas e impedem a descoberta de aplicações instaladas.  |   Nas definições de 'Controlo de Conta de Utilizador' no servidor, configurar a definição UAC para estar num dos dois níveis mais baixos.
9010: O VM está desligado.     |   O VM está desligado.  |   Certifique-se de que o VM está ligado.
9011: Ficheiro de metadados descoberto não encontrado no sistema de ficheiros VM do hóspede.    |   O problema pode ocorrer devido a um erro interno.   |   O problema deve resolver-se automaticamente em 24 horas. Se o problema persistir, contacte o Microsoft Support.
9012: O ficheiro de metadados descoberto está vazio.     |   O problema pode ocorrer devido a um erro interno.   |   O problema deve resolver-se automaticamente em 24 horas. Se o problema persistir, contacte o Microsoft Support.
9013: É criado um novo perfil temporário para cada login.    |   É criado um novo perfil temporário para cada login no VMware VM.    |   Contacte o Microsoft Support para obter uma resolução.
9014: Não é possível recuperar metadados do sistema de ficheiros VM do hóspede.     |   Sem conectividade com o anfitrião ESXi    |   Certifique-se de que o aparelho pode ligar-se à porta 443 no hospedeiro ESXi que executa o VM
9015: A função de Operações de Hóspedes não está ativada na conta de utilizador do vCenter   |   A função de Operações de Hóspedes não está ativada na conta de utilizador do vCenter.   |   Certifique-se de que a função de Operações de Hóspedes está ativada na conta de utilizador do vCenter.
9016: Incapaz de descobrir como agente de operações de hóspedes está desatualizado.   |   As ferramentas Vmware não estão corretamente instaladas ou não estão atualizadas.    |   Certifique-se de que as ferramentas VMware estão corretamente instaladas e atualizadas.
9017: O ficheiro com metadados descobertos não é encontrado no VM.  |   O problema pode ocorrer devido a um erro interno.   |   Contacte o Microsoft Support para obter uma resolução.
9018: O PowerShell não está instalado nos VMs do Hóspede.  |   O PowerShell não está disponível no VM convidado.    |   Instale o PowerShell no VM convidado.
9019: Incapaz de descobrir devido a falhas de operação VM convidadas.     |   A operação VMware Guest falhou no VM.    |   Certifique-se de que as credenciais VM são válidas e o nome de utilizador fornecido nas credenciais VM do hóspede está em formato UPN.
9020: A permissão de criação de ficheiros é negada.    |   A função associada ao utilizador ou à política de grupo está a restringir o utilizador de criar o ficheiro em pasta    |   Verifique se o utilizador convidado fornecido criou permissão para o ficheiro na pasta. Consulte **notificações** na Avaliação do Servidor para o nome da pasta.
9021: Incapaz de criar ficheiro no caminho temp do sistema.     |   A ferramenta VMware reporta o caminho temp do sistema em vez do Caminho Temporário dos Utilizadores.    |   Atualize a versão da ferramenta vmware acima de 10287 (formato NGC/VI Cliente).
9022: O acesso ao objeto WMI é negado.    |   A função associada ao utilizador ou à política de grupo está a restringir o utilizador a aceder ao objeto WMI.  |   Entre em contato com o Microsoft Support.
9023: Incapaz de executar PowerShell como o valor variável ambiente SystemRoot está vazio.    |   O valor da variável ambiente SystemRoot está vazio para o VM convidado.     |   Contacte o Microsoft Support para obter uma resolução.
9024: O valor variável do ambiente TEMP está vazio.    |   O valor da variável ambiente TEMP está vazio para o VM convidado.   |   Entre em contato com o Microsoft Support.
9025: PowerShell é corrompido nos VMs de hóspedes.  |   PowerShell está corrompido no VM convidado.    |   Volte a instalar o PowerShell no VM do hóspede e verifique se o PowerShell pode ser executado no VM convidado.
9026: Incapaz de executar operações de hóspedes na VM.  |   O estado VM não permite que as operações dos hóspedes sejam executadas no VM.   |   Contacte o Microsoft Support para obter uma resolução.
9027: O agente de operações de hóspedes não está a funcionar na VM.   |   Não contactou o agente de operações do hóspede que funciona dentro da máquina virtual.    |   Contacte o Microsoft Support para obter uma resolução.
9028: O ficheiro não pode ser criado devido a um armazenamento insuficiente de discos em VM.     |   Não há espaço suficiente no disco.   |   Certifique-se de que existe espaço suficiente no armazenamento do disco do VM.
9029: Sem acesso a powershell na credencial VM fornecida.   |   O acesso a Powershell não está disponível para o utilizador.     |   Certifique-se de que o utilizador adicionado no aparelho pode aceder ao PowerShell no VM do hóspede.
9030: Não é possível recolher metadados descobertos à medida que o hospedeiro ESXi está desligado.     |   O hospedeiro ESXi está num estado desligado.   |   Certifique-se de que o anfitrião ESXi que executa o VM está ligado.
9031: Não é possível recolher metadados descobertos, uma vez que o hospedeiro ESXi não está a responder.   |   O hospedeiro remoto está em estado inválido.    |   Certifique-se de que o anfitrião ESXi que executa o VM está a funcionar e ligado.
9032: Incapaz de descobrir devido a um erro interno.   |   O problema pode ocorrer devido a um erro interno.   |   Contacte o Microsoft Support para obter uma resolução.
9033: Não é possível descobrir como o nome de utilizador VM contém caracteres inválidos.     |   Os caracteres inválidos foram detetados no nome de utilizador.   |   Forneça novamente a credencial VM, garantindo que não existem caracteres inválidos.
9034: O nome de utilizador fornecido não está no formato UPN.    |   O nome de utilizador não está no formato UPN.  |   Certifique-se de que o nome de utilizador está no formato Nome Principal do Utilizador (UPN).
9035: Não é possível descobrir como o modo linguagem Powershell não está definido como "Linguagem Completa".  |   O modo linguístico para Powershell no VM convidado não está definido para linguagem completa.   |   Certifique-se de que o modo de linguagem PowerShell está definido para "Linguagem Completa".
9037: A recolha de dados parou temporariamente, uma vez que o tempo de resposta em VM é demasiado elevado.    |   O VM descoberto está a demorar muito tempo a responder     |   Nenhuma ação necessária. Uma nova tentativa será tentada em 24 horas para a descoberta da aplicação e 3 horas para análise de dependência (sem agente).
1000: O tipo de sistema operativo não é suportado.   |   O sistema operativo que funciona no servidor não é nem Windows nem Linux.    |   Os tipos de sistema operativo suportado são apenas Windows e Linux.
10001: O script para a descoberta do servidor não é encontrado no aparelho.    |   A descoberta não está a funcionar como esperado.   |   Contacte o Microsoft Support para obter uma resolução.
10002: A tarefa da Discovery não foi concluída a tempo.     |   O agente discovery não está a funcionar como esperado.     |   O problema deve resolver-se automaticamente em 24 horas. Se o problema persistir, contacte o Microsoft Support.
10003: Processo de execução da tarefa de descoberta saiu com um erro.    |   Processo de execução da tarefa de descoberta saiu com um erro.  |   O problema deve resolver-se automaticamente em 24 horas. Se o problema ainda persistir, contacte o Microsoft Support.
10004: Credencial não prevista para o tipo de sistema operativo convidado.  |   As credenciais de acesso a máquinas deste tipo de SO não foram fornecidas no aparelho Azure Migrate.    |   Adicione credenciais para máquinas no aparelho
10005: As credenciais fornecidas não são válidas.   |   As credenciais fornecidas para o aparelho aceder ao servidor estão incorretas.  |   Atualize as credenciais fornecidas no aparelho e certifique-se de que o servidor está acessível utilizando as credenciais.
10006: Tipo de SO de hóspede não suportado por loja de credenciais.  |   O sistema operativo que funciona no servidor não é nem Windows nem Linux.    |   Os tipos de sistema operativo suportado são apenas Windows e Linux.
10007: Incapaz de processar os metadados descobertos.    |   Erro ocorreu ao tentar deserizar o JSON.    |   Contacte o Microsoft Support para obter uma resolução.
10008: Não é possível criar um ficheiro no servidor.    |  O problema pode ocorrer devido a um erro interno.    |   Contacte o Microsoft Support para obter uma resolução.
10009: Não é possível escrever metadados descobertos num ficheiro no servidor.  |   O problema pode ocorrer devido a um erro interno.   |   Contacte o Microsoft Support para obter uma resolução.




## <a name="next-steps"></a>Passos seguintes
Configurar um aparelho para [VMware,](how-to-set-up-appliance-vmware.md) [Hiper-V](how-to-set-up-appliance-hyper-v.md)ou [servidores físicos](how-to-set-up-appliance-physical.md).