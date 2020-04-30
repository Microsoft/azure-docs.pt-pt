---
title: Implantação e descoberta de aparelhos migratórios Azure
description: Obtenha ajuda na implementação do aparelho Azure Migrate e na descoberta de máquinas.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 6cb83a87f2e96eb62696e5d92095ef2b8d7c7def
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81677327"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Problemas de resolução do aparelho e descoberta do Azure Migrate

Este artigo ajuda-o a resolver problemas ao implantar o aparelho [Azure Migrate](migrate-services-overview.md) e a utilizar o aparelho para descobrir máquinas no local.


## <a name="whats-supported"></a>O que é suportado?

[Reveja](migrate-appliance.md) os requisitos de suporte do aparelho.


## <a name="invalid-ovf-manifest-entry"></a>"Entrada manifesto OVF inválida"

Se receber o erro "O ficheiro manifesto fornecido é inválido: entrada manifesto OVF inválida", faça o seguinte:

1. Verifique se o ficheiro OVA do eletrodoméstico Azure é descarregado corretamente verificando o seu valor de haxixe. [Saiba mais](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Se o valor do haxixe não corresponder, baixe novamente o ficheiro OVA e volte a tentar a implementação.
2. Se a implementação ainda falhar, e estiver a usar o cliente VMware vSphere para implementar o ficheiro OVF, tente implementá-lo através do cliente web vSphere. Se a implementação ainda falhar, tente utilizar um navegador web diferente.
3. Se estiver a utilizar o cliente web da vSphere e a tentar implementá-lo no vCenter Server 6.5 ou 6.7, tente implementar o OVA diretamente no hospedeiro ESXi:
   - Ligue-se diretamente ao anfitrião ESXi (em vez do VCenter Server) com o cliente web (https://<endereço IP de *anfitrião*>/ui).
   - No**Inventário** **Inicial,** > selecione modelo**OVF de implementação**de **ficheiros** > . Navegue no OVA e complete a implementação.
4. Se a implantação ainda falhar, contacte o suporte da Migração Azure.

## <a name="cant-connect-to-the-internet"></a>Não se pode ligar à internet

Isto pode acontecer se a máquina do aparelho estiver atrás de um proxy.

- Certifique-se de fornecer as credenciais de autorização se o representante precisar delas.
- Se estiver a utilizar um representante de firewall baseado em URL para controlar a conectividade de saída, adicione [estes URLs](migrate-appliance.md#url-access) a uma lista de autorizações.
- Se estiver a utilizar um representante de interceção para se ligar à internet, importe o certificado de procuração para o VM do aparelho utilizando [estes passos](https://docs.microsoft.com/azure/migrate/concepts-collector).

## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Não pode assinar no Azure a partir da aplicação web do aparelho

O erro "Desculpe, mas estamos a ter problemas em inscrevê-lo" aparece se estiver a usar a conta incorreta do Azure para assinar no Azure. Este erro ocorre por um par de razões:

- Se iniciar sessão na aplicação web do aparelho para a nuvem pública, utilizando credenciais de conta de utilizador para o portal de nuvem do Governo.
- Se iniciar sessão na aplicação web do aparelho para a nuvem governamental utilizando credenciais de conta de utilizador para o portal privado de nuvem.

Certifique-se de que está a usar as credenciais corretas.

##  <a name="datetime-synchronization-error"></a>Erro de sincronização data/hora

Um erro sobre a sincronização da data e da hora (802) indica que o relógio do servidor pode estar fora de sincronização com o tempo atual em mais de cinco minutos. Altere o tempo de tempo no VM do coletor para corresponder ao tempo atual:

1. Abra um pedido de comando administrativo no VM.
2. Para verificar o fuso horário, corra **w32tm /tz**.
3. Para sincronizar o tempo, executar **w32tm /resincronização**.


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Se tiver este erro de ligação, poderá não conseguir ligar-se ao vCenter *Server Servername*.com:9443. Os detalhes do erro indicam que não `https://\*servername*.com:9443/sdk` há ponto final de escuta que possa aceitar a mensagem.

- Verifique se está a executar a versão mais recente do aparelho. Se não estiver, atualize o aparelho para a [versão mais recente](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Se o problema ainda ocorrer na versão mais recente, o aparelho poderá não conseguir resolver o nome de servidor de vCenter especificado, ou a porta especificada pode estar errada. Por defeito, se a porta não for especificada, o coletor tentará ligar-se à porta número 443.

    1. Ping *Servername*.com do aparelho.
    2. Se o passo 1 falhar, tente ligar-se ao servidor vCenter utilizando o endereço IP.
    3. Identifique o número de porta correto para ligar ao VCenter Server.
    4. Verifique se o VCenter Server está a funcionar.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Erro 60052/60039: O aparelho pode não estar registado

- Erro 60052: "O aparelho pode não ser registado com sucesso no projeto Azure Migrate" ocorre se a conta Azure utilizada para registar o aparelho não tiver permissões insuficientes.
    - Certifique-se de que a conta de utilizador Azure utilizada para registar o aparelho tem, pelo menos, permissões contributivas na subscrição.
    - [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sobre as funções e permissões necessárias do Azure.
- Erro 60039: "O aparelho pode não ser registado com sucesso no projeto Azure Migrate" pode ocorrer se o registo falhar porque o projeto Azure Migrate utilizado para o registo do aparelho não pode ser encontrado.
    - No portal Azure e verificar se o projeto existe no grupo de recursos.
    - Se o projeto não existir, crie um novo projeto Azure Migrate no seu grupo de recursos e registe o aparelho novamente. [Aprenda a](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) criar um novo projeto.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Erro 60030/60031: Operação de gestão do cofre falhou

Se receber o erro 60030 ou 60031, "Uma operação de gestão do Cofre chave Azure falhou", faça o seguinte:
- Certifique-se de que a conta de utilizador Azure utilizada para registar o aparelho tem, pelo menos, permissões contributivas na subscrição.
- Certifique-se de que a conta tem acesso ao cofre da chave especificado na mensagem de erro e, em seguida, tente novamente a operação.
- Se o problema persistir, contacte o suporte da Microsoft.
- [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sobre as funções e permissões necessárias do Azure.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Erro 60028: Descoberta não pôde ser iniciada

Erro 60028: "A descoberta não pôde ser iniciada por causa de um erro. A operação falhou na lista especificada de anfitriões ou clusters" indica que a descoberta não poderia ser iniciada nos anfitriões listados no erro devido a um problema no acesso ou recuperação de informações vm. Os restantes anfitriões foram adicionados com sucesso.

- Adicione novamente os anfitriões listados no erro, utilizando a opção Adicionar o **anfitrião.**
- Se houver um erro de validação, reveja a orientação de reparação para corrigir os erros e, em seguida, tente a opção **Salvar e reinicie** a descoberta novamente.

## <a name="error-60025-azure-ad-operation-failed"></a>Erro 60025: Operação da AD Azure falhou 
Erro 60025: "Uma operação da AD Azure falhou. O erro ocorreu ao criar ou atualizar a aplicação Azure AD" ocorre quando a conta de utilizador azure utilizada para iniciar a descoberta é diferente da conta utilizada para registar o aparelho. Efetue uma das seguintes ações:

- Certifique-se de que a conta do utilizador que inicia a descoberta é a mesma utilizada para registar o aparelho.
- Forneça permissões de acesso à aplicação Azure Ative Directory à conta do utilizador para a qual a operação de descoberta está a falhar.
- Elimine o grupo de recursos anteriormente criado para o projeto Azure Migrate. Crie outro grupo de recursos para recomeçar.
- [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sobre as permissões de aplicação do Azure Ative Directory.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Erro 50004: Não pode ligar-se ao hospedeiro ou ao cluster

Erro 50004: "Não pode ligar-se a um hospedeiro ou cluster porque o nome do servidor não pode ser resolvido. Código de erro WinRM: 0x803381B9" pode ocorrer se o serviço Azure DNS para o aparelho não conseguir resolver o cluster ou o nome do hospedeiro que forneceu.

- Se vir este erro no cluster, cluster FQDN.
- Também pode ver este erro para os anfitriões num aglomerado. Isto indica que o aparelho pode ligar-se ao cluster, mas o cluster devolve nomes hospedeiros que não são FQDNs. Para resolver este erro, atualize o ficheiro dos anfitriões no aparelho adicionando um mapeamento do endereço IP e nomes do anfitrião:
    1. Abra o bloco de notas como administrador.
    2. Abra o ficheiro C:\Windows\System32\Drivers\etc\hosts.
    3. Adicione o endereço IP e o nome do anfitrião em sequência. Repita para cada hospedeiro ou aglomerado onde vê este erro.
    4. Guarde e feche o ficheiro hosts.
    5. Verifique se o aparelho pode ligar-se aos anfitriões, utilizando a aplicação de gestão de aparelhos. Após 30 minutos, deverá ver as últimas informações para estes anfitriões no portal Azure.

## <a name="discovered-vms-not-in-portal"></a>VMs descobertos não no portal

Se o estado de descoberta é "Discovery em progresso", mas ainda não veja os VMs no portal, espere alguns minutos:
- Leva cerca de 15 minutos para um VMware VM .
- Leva cerca de dois minutos para cada anfitrião adicionado para a descoberta de Hyper-V VM.

Se esperar e o estado não mudar, selecione **Refresh** no separador **Servidores.** Isto deve mostrar a contagem dos servidores descobertos em Azure Migrate: Server Assessment e Azure Migrate: Server Migration.

Se isto não funcionar e estiver a descobrir servidores VMware:

- Verifique se a conta vCenter que especificou tem permissões definidas corretamente, com acesso a pelo menos um VM.
- O Azure Migrate não consegue descobrir VMware VMs se a conta vCenter tiver acesso concedido ao nível da pasta VCenter VM. [Saiba mais](set-discovery-scope.md) sobre a descoberta de scoping.

## <a name="vm-data-not-in-portal"></a>Dados vM não no portal

Se os VM saem descobertos não aparecem no portal ou se os dados vm estão desatualizados, aguarde alguns minutos. Leva até 30 minutos para que as alterações nos dados de configuração VM descobertos apareçam no portal. Pode levar algumas horas para que as alterações nos dados da aplicação apareçam. Se não houver dados depois deste tempo, tente refrescar,como se segue

1. Nos **servidores** > **Azure Migrate Server Assessment**, selecione visão **geral**.
2. Em **'Gerir',** selecione **Agent Health**.
3. Selecione **Agente Refresh**.
4. Aguarde a operação de atualização. Agora deve ver informações atualizadas.

## <a name="deleted-vms-appear-in-portal"></a>VMs apagados aparecem no portal

Se apagar os VMs e eles ainda aparecerem no portal, aguarde 30 minutos. Se ainda aparecerem, afree-se como acima descrito.

## <a name="common-app-discovery-errors"></a>Erros comuns de descoberta de aplicativos

A Azure Migrate apoia a descoberta de aplicações, funções e funcionalidades, utilizando o Azure Migrate: Server Assessment. A descoberta de aplicativos é atualmente suportada apenas para VMware. [Saiba mais](how-to-discover-applications.md) sobre os requisitos e passos para a criação de apps.

Erros típicos de descoberta de aplicativos são resumidos na tabela. 

**Erro** | **Motivo** | **Ação**
--- | --- | --- | ---
10000: "Incapaz de descobrir as aplicações instaladas no servidor". | Isto pode ocorrer se o sistema operativo da máquina não for Windows ou Linux. | Utilize apenas a descoberta de aplicativos para Windows/Linux.
10001: "Incapaz de recuperar as aplicações instaladas no servidor". | Erro interno - alguns ficheiros em falta no aparelho. | Contacte o Suporte da Microsoft.
10002: "Incapaz de recuperar as aplicações instaladas no servidor". | O agente de descoberta do aparelho pode não estar a funcionar corretamente. | Se o problema não se resolver dentro de 24 horas, contacte o suporte.
10003 "Incapaz de recuperar as aplicações instaladas no servidor". | O agente de descoberta do aparelho pode não estar a funcionar corretamente. | Se o problema não se resolver dentro de 24 horas, contacte o suporte.
10004: "Incapaz de descobrir aplicações instaladas para <máquinas> Windows/Linux." |  As credenciais de acesso <Windows/Linux> máquinas não foram fornecidas no aparelho.| Adicione uma credencial ao aparelho que tenha acesso às máquinas <Windows/Linux>.
10005: "Incapaz de aceder ao servidor no local". | As credenciais de acesso podem estar erradas. | Atualize as credenciais do aparelho certifique-se de que pode aceder à máquina relevante com elas. 
10006: "Incapaz de aceder ao servidor no local". | Isto pode ocorrer se o sistema operativo da máquina não for Windows ou Linux.|  Utilize apenas a descoberta de aplicativos para Windows/Linux.
10007: "Incapaz de processar os metadados recuperados" | Este erro interno ocorreu enquanto tentava desserializar a JSON | Entrar em contato com o Suporte da Microsoft para uma resolução
9000/9001/9002: "Incapaz de descobrir as aplicações instaladas no servidor". | As ferramentas VMware podem não ser instaladas ou corrompidas. | Instale/volte a instalar as ferramentas VMware na máquina relevante e verifique se está a funcionar.
9003: Incapaz de descobrir as aplicações instaladas no servidor". | Isto pode ocorrer se o sistema operativo da máquina não for Windows ou Linux. | Utilize apenas a descoberta de aplicativos para Windows/Linux.
9004: "Incapaz de descobrir as aplicações instaladas no servidor". | Isto pode acontecer se o VM estiver desligado. | Para ser descoberto, certifique-se de que o VM está ligado.
9005: "Incapaz de descobrir as aplicações instaladas no VM. | Isto pode ocorrer se o sistema operativo da máquina não for Windows ou Linux. | Utilize apenas a descoberta de aplicativos para Windows/Linux.
9006/9007: "Incapaz de recuperar as aplicações instaladas no servidor". | O agente de descoberta do aparelho pode não estar a funcionar corretamente. | Se o problema não se resolver dentro de 24 horas, contacte o suporte.
9008: "Incapaz de recuperar as aplicações instaladas no servidor". | Pode ser um erro interno.  | Tf o problema não se resolve dentro de 24 horas, suporte de contato.
9009: "Incapaz de recuperar as aplicações instaladas no servidor". | Pode ocorrer se as definições de Controlo de Conta de Utilizador do Windows (UAC) no servidor forem restritivas e impedir a descoberta de aplicações instaladas. | Procure as definições de 'Controlo de Conta de Utilizador' no servidor e configure a definição do UAC no servidor para um dos dois níveis mais baixos.
9010: "Incapaz de recuperar as aplicações instaladas no servidor". | Pode ser um erro interno.  | Tf o problema não se resolve dentro de 24 horas, suporte de contato.
9011: "File to download from guest is not found on the guest VM" | O problema pode ocorrer devido a um erro interno. | A questão deve ser resolvida automaticamente em 24 horas. Se o problema persistir, contacte o Microsoft Support.
9012: "O conteúdo do ficheiro do resultado está vazio." | O problema pode ocorrer devido a um erro interno. | A questão deve ser resolvida automaticamente em 24 horas. Se o problema persistir, contacte o Microsoft Support.
9013: "É criado um novo perfil temporário para cada login no VMware VM" | Um novo perfil temporário é criado para cada login no VM | Certifique-se de que o nome de utilizador fornecido nas credenciais VM do hóspede está no formato UPN.
9015: "Incapaz de ligar aos VMware VMs devido a privilégios insuficientes no vCenter" | A função de Operações de Hóspedes não está ativada na conta de utilizador vCenter | Certifique-se de que a função de Operações de Hóspedes está ativada na conta de utilizador vCenter.
9016: "Incapaz de ligar aos VMware VMs como o agente de operações convidado está fora de dados" | As ferramentas VMware não estão corretamente instaladas ou não estão atualizadas. | Certifique-se de que as ferramentas VMware estão corretamente instaladas e atualizadas.
9017: "Ficheiro com metadados descobertos não é encontrado no VM." | O problema pode ocorrer devido a um erro interno. | Contacte o Microsoft Support para obter uma resolução.
9018: "PowerShell não está instalado nos VMs convidados." | A PowerShell não está disponível no VM convidado. | Instale a PowerShell no VM convidado.
9019: "Incapaz de descobrir devido a falhas na operação VM convidadas" | A operação de hóspedes vMware falhou no VM. | Certifique-se de que as credenciais VM são válidas e o nome de utilizador fornecido nas credenciais VM do hóspede está no formato UPN.
9020: "A autorização de criação de ficheiros é negada." | A função associada ao utilizador ou à política do grupo é restringir o utilizador a criar o ficheiro na pasta | Verifique se o utilizador convidado fornecido criou permissão para o ficheiro na pasta. Consulte **notificações** na Avaliação do Servidor para o nome da pasta.
9021: "O ficheiro criar permissão é negado na pasta System Temp Path." | A versão da ferramenta VMware no VM não é suportada | Atualize a sua versão de ferramenta VMware acima de 10.2.0.
9022: "O acesso ao objeto WMI é negado." | A função associada ao utilizador ou à política do grupo está a restringir o utilizador a aceder ao objeto WMI. | Contacte o Suporte da Microsoft.
9023: "SystemRoot ambiente valor variável está vazio." | Não é conhecido | Contacte o Suporte da Microsoft.
9024: "O valor variável do ambiente temp está vazio." | Não é conhecido | Contacte o Suporte da Microsoft.
9025: "PowerShell é corrupto nos VMs convidados." | Não é conhecido | Reinstale a PowerShell no VM de hóspedes e verifique se a PowerShell pode ser executada no VM de hóspedes.
8084: "Incapaz de descobrir aplicações devido a <Exception from VMware>erro de VMware: " | O aparelho Azure Migrate utiliza APIs VMware para descobrir aplicações. Este problema pode acontecer se uma exceção for lançada pelo vCenter Server enquanto tenta descobrir aplicações. A mensagem de falha da VMware é exibida na mensagem de erro mostrada no portal. | Procure a mensagem na [documentação vMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)e siga os passos a corrigir. Se não conseguir reparar, contacte o suporte da Microsoft.



## <a name="next-steps"></a>Passos seguintes
Instale um aparelho para [VMware,](how-to-set-up-appliance-vmware.md) [Hiper-V](how-to-set-up-appliance-hyper-v.md)ou [servidores físicos](how-to-set-up-appliance-physical.md).
