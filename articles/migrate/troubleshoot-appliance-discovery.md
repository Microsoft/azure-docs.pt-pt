---
title: Resolução de problemas Azure Migrar implantação e descoberta de aparelhos
description: Obtenha ajuda na implantação do aparelho Azure Migrate e na descoberta de máquinas.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 14503299b241b87459517818b0d0b2be6dde9072
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219239"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Resolução de problemas do aparelho Esmigósia Azure Migrar e descoberta

Este artigo ajuda-o a resolver problemas ao colocar o aparelho [Azure Migrate](migrate-services-overview.md) e a utilizar o aparelho para descobrir máquinas no local.


## <a name="whats-supported"></a>O que é suportado?

[Reveja](migrate-appliance.md) os requisitos de suporte do aparelho.


## <a name="invalid-ovf-manifest-entry"></a>"Entrada manifesto OVF inválida"

Se receber o erro "O ficheiro manifesto fornecido é inválido: Entrada manifesto OVF inválida", faça o seguinte:

1. Verifique se o ficheiro OVA do aparelho Azure Migrate é descarregado corretamente, verificando o seu valor de haxixe. [Saiba mais](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Se o valor do haxixe não corresponder, descarregue novamente o ficheiro OVA e volte a tentar a implementação.
2. Se a implementação ainda falhar, e estiver a utilizar o cliente VMware vSphere para implementar o ficheiro OVF, tente implantá-lo através do cliente web vSphere. Se a implementação ainda falhar, tente utilizar um navegador web diferente.
3. Se estiver a utilizar o cliente web vSphere e a tentar implantá-lo no vCenter Server 6.5 ou 6.7, tente implantar o OVA diretamente no anfitrião ESXi:
   - Ligue-se diretamente ao anfitrião ESXi (em vez do vCenter Server) com o cliente web (https://<*endereço IP anfitrião*>/ui).
   - No **Inventário Doméstico**,  >  **Inventory**selecione o modelo de OVF de **File**  >  **implementação de ficheiros**. Navegue pelo OVA e complete a colocação.
4. Se a implantação ainda falhar, contacte o suporte da Azure Migrate.

## <a name="cant-connect-to-the-internet"></a>Não se liga à internet

Isto pode acontecer se a máquina do aparelho estiver por detrás de um representante.

- Certifique-se de fornecer as credenciais de autorização se o representante precisar delas.
- Se estiver a usar um representante de firewall baseado em URL para controlar a conectividade de saída, adicione [estes URLs](migrate-appliance.md#url-access) a uma lista de indicações.
- Se estiver a utilizar um representante de interceção para ligar à internet, importe o certificado de procuração para o VM do aparelho utilizando [estes passos](https://docs.microsoft.com/azure/migrate/concepts-collector).

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

Se obter este erro de ligação, poderá não conseguir ligar-se ao nome do *servidor*do servidor do vCenter Server .com:9443. Os detalhes do erro indicam que não há nenhum ponto final a ouvir `https://\*servername*.com:9443/sdk` que possa aceitar a mensagem.

- Verifique se está a executar a versão mais recente do aparelho. Se não estiver, atualize o aparelho para a [versão mais recente](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Se o problema ainda ocorrer na versão mais recente, o aparelho poderá não conseguir resolver o nome do servidor vCenter especificado, ou a porta especificada pode estar errada. Por predefinição, se a porta não for especificada, o coletor tentará ligar-se ao número da porta 443.

    1. Ping *Servername.com*do aparelho.
    2. Se o passo 1 falhar, tente ligar-se ao servidor vCenter utilizando o endereço IP.
    3. Identifique o número de porta correto para ligar ao servidor vCenter.
    4. Verifique se o vCenter Server está a funcionar.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Erro 60052/60039: O aparelho pode não estar registado

- Erro 60052: "O aparelho pode não ser registado com sucesso no projeto Azure Migrate" ocorrer se a conta Azure utilizada para registar o aparelho não tiver permissões suficientes.
    - Certifique-se de que a conta de utilizador Azure utilizada para registar o aparelho tem pelo menos permissões do Contribuinte na subscrição.
    - [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sobre as funções e permissões necessárias do Azure.
- Erro 60039: "O aparelho pode não ser registado com sucesso no projeto Azure Migrate" pode ocorrer se o registo falhar porque o projeto Azure Migrate usado para o registo do aparelho não pode ser encontrado.
    - No portal Azure e verifique se o projeto existe no grupo de recursos.
    - Se o projeto não existir, crie um novo projeto Azure Migrate no seu grupo de recursos e registe novamente o aparelho. [Saiba como](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) criar um novo projeto.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Erro 60030/60031: A operação de gestão do Cofre falhou

Se receber o erro 60030 ou 60031, "Uma operação de gestão do Cofre da Chave Azure falhou", faça o seguinte:
- Certifique-se de que a conta de utilizador Azure utilizada para registar o aparelho tem pelo menos permissões do Contribuinte na subscrição.
- Certifique-se de que a conta tem acesso ao cofre de chaves especificado na mensagem de erro e, em seguida, re-tentar a operação.
- Se o problema persistir, contacte o suporte da Microsoft.
- [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sobre as funções e permissões necessárias do Azure.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Erro 60028: A descoberta não pôde ser iniciada

Erro 60028: "A descoberta não pôde ser iniciada por causa de um erro. A operação falhou na lista especificada de anfitriões ou clusters" indica que a descoberta não pôde ser iniciada nos anfitriões listados no erro devido a um problema no acesso ou recuperação de informações em VM. Os restantes anfitriões foram adicionados com sucesso.

- Adicione novamente os anfitriões listados no erro, utilizando a opção **adicionar.**
- Se houver um erro de validação, reveja a orientação de remediação para corrigir os erros e, em seguida, tente a opção **Salvar e iniciar novamente** a opção de descoberta.

## <a name="error-60025-azure-ad-operation-failed"></a>Erro 60025: A operação AD Azure falhou 
Erro 60025: "Uma operação AD Azure falhou. O erro ocorreu ao criar ou atualizar a aplicação AZure AD" ocorre quando a conta de utilizador Azure utilizada para iniciar a descoberta é diferente da conta utilizada para registar o aparelho. Faça um dos seguintes:

- Certifique-se de que a conta de utilizador que inicia a descoberta é a mesma que foi utilizada para registar o aparelho.
- Fornecer permissões de acesso à aplicação Azure Ative para a conta do utilizador para as quais a operação de descoberta está a falhar.
- Elimine o grupo de recursos anteriormente criado para o projeto Azure Migrate. Criar outro grupo de recursos para recomeçar.
- [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance---vmware) sobre as permissões de candidatura do Azure Ative Directory.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Erro 50004: Não se pode ligar ao hospedeiro ou ao cluster

Erro 50004: "Não é possível ligar-se a um hospedeiro ou cluster porque o nome do servidor não pode ser resolvido. O código de erro winRM: 0x803381B9" pode ocorrer se o serviço Azure DNS para o aparelho não conseguir resolver o cluster ou o nome de anfitrião que forneceu.

- Se vir este erro no cluster, cluster FQDN.
- Você também pode ver este erro para os anfitriões em um cluster. Isto indica que o aparelho pode ligar-se ao cluster, mas o cluster devolve nomes de anfitriões que não são FQDNs. Para resolver este erro, atualize o ficheiro dos anfitriões no aparelho adicionando um mapeamento do endereço IP e dos nomes dos anfitriões:
    1. Abra o Bloco de Notas como administrador.
    2. Abra o ficheiro C:\Windows\System32\Drivers\etc\hosts.
    3. Adicione o endereço IP e o nome de anfitrião em seguida. Repita para cada hospedeiro ou cluster onde vê este erro.
    4. Guarde e feche o ficheiro hosts.
    5. Verifique se o aparelho pode ligar-se aos anfitriões, utilizando a aplicação de gestão do aparelho. Após 30 minutos, deverá ver as últimas informações destes anfitriões no portal Azure.

## <a name="discovered-vms-not-in-portal"></a>VMs descobertos não no portal

Se o estado de descoberta é "Discovery in progress", mas ainda não ver os VMs no portal, espere alguns minutos:
- Demora cerca de 15 minutos para um VMware VM.
- Leva cerca de dois minutos para cada hospedeiro adicionado para a descoberta do Hyper-V.

Se esperar e o estado não mudar, **selecione Refresh** no **separador Servidores.** Isto deve mostrar a contagem dos servidores descobertos em Azure Migrate: Server Assessment e Azure Migrate: Server Migration.

Se isto não funcionar e estiver a descobrir servidores VMware:

- Verifique se a conta vCenter especificada tem permissões corretamente definidas, com acesso a pelo menos um VM.
- A Azure Migrate não pode descobrir VMware VMs se a conta vCenter tiver acesso concedido ao nível da pasta VM do vCenter. [Saiba mais](set-discovery-scope.md) sobre a descoberta de scoping.

## <a name="vm-data-not-in-portal"></a>Dados VM não no portal

Se os VM descobertos não aparecerem no portal ou se os dados de VM estiverem desatualizados, aguarde alguns minutos. Leva até 30 minutos para que as alterações nos dados de configuração VM descobertos apareçam no portal. Pode levar algumas horas para que apareçam alterações nos dados da aplicação. Se não houver dados depois deste tempo, tente refrescar, da seguinte forma.

1. Na Avaliação **do**  >  **Servidor Azure Migrate,** selecione **Overview**.
2. Under **Manage**, selecione **Agent Health**.
3. Selecione **Refresh agent**.
4. Aguarde que a operação de atualização esteja concluída. Deve agora ver informações atualizadas.

## <a name="deleted-vms-appear-in-portal"></a>VMs eliminados aparecem no portal

Se apagar VMs e ainda aparecerem no portal, aguarde 30 minutos. Se ainda aparecerem, refresque-se como descrito acima.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Erro: O ficheiro carregado não está no formato esperado
Algumas ferramentas têm configurações regionais que criam o ficheiro CSV com o ponto mais alto como um delimiter. Por favor, altere as definições para garantir que o delimiter é uma vírgula.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Importei um CSV, mas vejo que "A Descoberta está em andamento"
Este estado aparece se o seu upload CSV falhou devido a uma falha de validação. Tente importar o CSV de novo. Pode descarregar o relatório de erro do upload anterior e seguir a orientação de remediação no ficheiro para corrigir os erros. O relatório de erro pode ser descarregado a partir da secção 'Detalhes de importação' na página 'Descubra máquinas'.

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>Não ver detalhes da aplicação mesmo depois de atualizar credenciais de hóspedes
A descoberta da aplicação é uma vez a cada 24 horas. Se quiser ver os detalhes imediatamente, refresque-se da seguinte forma. Isto pode levar alguns minutos dependendo do não. de VMs descobertos.

1. Na Avaliação **do**  >  **Servidor Azure Migrate,** selecione **Overview**.
2. Under **Manage**, selecione **Agent Health**.
3. Selecione **Refresh agent**.
4. Aguarde que a operação de atualização esteja concluída. Deve agora ver informações atualizadas.

## <a name="unable-to-export-application-inventory"></a>Inventário de pedidos de exportação incapaz de exportar
Certifique-se de que o utilizador que descarrega o inventário do portal tem privilégios de Contribuinte na subscrição.

## <a name="common-app-discovery-errors"></a>Erros comuns de descoberta de aplicativos

A Azure Migrate suporta a descoberta de aplicações, funções e funcionalidades, utilizando Azure Migrate: Server Assessment. A descoberta de aplicações é atualmente suportada apenas para VMware. [Saiba mais](how-to-discover-applications.md) sobre os requisitos e passos para a criação de apps.

Erros típicos de descoberta de aplicações são resumidos na tabela. 

**Erro** | **Motivo** | **Ação**
--- | --- | --- 
1000: "Não é possível descobrir as aplicações instaladas no servidor". | Isto pode ocorrer se o sistema operativo da máquina não for Windows ou Linux. | Utilize apenas a descoberta de aplicativos para Windows/Linux.
10001: "Incapaz de recuperar as aplicações instaladas no servidor". | Erro interno - alguns ficheiros em falta no aparelho. | Contacte o Suporte da Microsoft.
10002: "Incapaz de recuperar as aplicações instaladas no servidor". | O agente de descoberta do aparelho pode não estar a funcionar corretamente. | Se o problema não se resolver dentro de 24 horas, contacte o suporte.
10003 "Não conseguir recuperar as aplicações instaladas no servidor". | O agente de descoberta do aparelho pode não estar a funcionar corretamente. | Se o problema não se resolver dentro de 24 horas, contacte o suporte.
10004: "Não é possível descobrir aplicações instaladas para <máquinas de> Windows/Linux." |  As credenciais de acesso <máquinas de> Windows/Linux não foram fornecidas no aparelho.| Adicione uma credencial ao aparelho que tenha acesso às máquinas de> Windows/Linux <.
10005: "Incapaz de aceder ao servidor no local". | As credenciais de acesso podem estar erradas. | Atualize as credenciais do aparelho certifique-se de que pode aceder à máquina relevante com eles. 
10006: "Incapaz de aceder ao servidor no local". | Isto pode ocorrer se o sistema operativo da máquina não for Windows ou Linux.|  Utilize apenas a descoberta de aplicativos para Windows/Linux.
10007: "Incapaz de processar os metadados recuperados" | Este erro interno ocorreu ao tentar deserizar o JSON | Contacte o Microsoft Support para obter uma resolução
9000/9001/9002: "Incapaz de descobrir as aplicações instaladas no servidor". | As ferramentas VMware podem não ser instaladas ou corrompidas. | Instale/reinstale as ferramentas VMware na máquina relevante e verifique se está a funcionar.
9003: Não é possível descobrir as aplicações instaladas no servidor". | Isto pode ocorrer se o sistema operativo da máquina não for Windows ou Linux. | Utilize apenas a descoberta de aplicativos para Windows/Linux.
9004: "Não é possível descobrir as aplicações instaladas no servidor". | Isto pode acontecer se o VM for desligado. | Para ser descoberto, certifique-se de que o VM está ligado.
9005: "Não foi possível descobrir as aplicações instaladas no VM. | Isto pode ocorrer se o sistema operativo da máquina não for Windows ou Linux. | Utilize apenas a descoberta de aplicativos para Windows/Linux.
9006/9007: "Incapaz de recuperar as aplicações instaladas no servidor". | O agente de descoberta do aparelho pode não estar a funcionar corretamente. | Se o problema não se resolver dentro de 24 horas, contacte o suporte.
9008: "Incapaz de recuperar as aplicações instaladas no servidor". | Pode ser um erro interno.  | Tf o problema não se resolve dentro de 24 horas, suporte de contato.
9009: "Incapaz de recuperar as aplicações instaladas no servidor". | Pode ocorrer se as definições de Controlo de Conta de Utilizador do Windows (UAC) no servidor forem restritivas e impedir a descoberta de aplicações instaladas. | Procure as definições de 'Controlo de Conta de Utilizador' no servidor e configufique a definição de UAC no servidor para um dos dois níveis inferiores.
9010: "Incapaz de recuperar as aplicações instaladas no servidor". | Pode ser um erro interno.  | Tf o problema não se resolve dentro de 24 horas, suporte de contato.
9011: "O ficheiro para descarregar do hóspede não é encontrado no VM do hóspede" | O problema pode ocorrer devido a um erro interno. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema ainda persistir, contacte o Microsoft Support.
9012: "O conteúdo do ficheiro de resultados está vazio." | O problema pode ocorrer devido a um erro interno. | O problema deve ser resolvido automaticamente em 24 horas. Se o problema ainda persistir, contacte o Microsoft Support.
9013: "É criado um novo perfil temporário para cada login no VMware VM" | É criado um novo perfil temporário para cada login no VM. | Certifique-se de que o nome de utilizador fornecido nas credenciais VM do hóspede está no formato UPN. Depois de alterar o nome de utilizador fornecido nas credenciais VM do hóspede para o formato UPN, em Task Manager no aparelho, reinicie o Microsoft Azure VMWare Discovery Service para encontrar a nova descoberta.
9015: "Incapaz de ligar aos VMware VMs devido a privilégios insuficientes no vCenter" | A função de Operações de Hóspedes não está ativada na conta de utilizador do vCenter. | Certifique-se de que a função de Operações de Hóspedes está ativada na conta de utilizador do vCenter.
9016: "Incapaz de ligar aos VMware VMs como o agente de operações de hóspedes está fora dos dados" | As ferramentas VMware não estão corretamente instaladas ou não estão atualizadas. | Certifique-se de que as ferramentas VMware estão corretamente instaladas e atualizadas.
9017: "O ficheiro com metadados descobertos não é encontrado no VM." | O problema pode ocorrer devido a um erro interno. | Contacte o Microsoft Support para obter uma resolução.
9018: "O PowerShell não está instalado nos VMs dos Hóspedes." | O PowerShell não está disponível no VM convidado. | Instale o PowerShell no VM convidado.
9019: "Incapaz de descobrir devido a falhas de operação VM de hóspedes" | A operação de hóspedes VMware falhou no VM. | Certifique-se de que as credenciais VM são válidas e o nome de utilizador fornecido nas credenciais VM do hóspede está em formato UPN.
9020: "A permissão de criação de ficheiros é negada." | A função associada ao utilizador ou à política de grupo está a restringir o utilizador a criar o ficheiro na pasta | Verifique se o utilizador convidado fornecido criou permissão para o ficheiro na pasta. Consulte **notificações** na Avaliação do Servidor para o nome da pasta.
9021: "A permissão de criação de ficheiros é negada na pasta Temp Path do sistema." | A versão da ferramenta VMware no VM não é suportada | Atualize a versão da ferramenta VMware acima de 10.2.0.
9022: "Obter acesso a objetos WMI é negado." | A função associada ao utilizador ou à política de grupo está a restringir o utilizador a aceder ao objeto WMI. | Entre em contato com o Microsoft Support.
9023: "O valor variável do ambiente SystemRoot está vazio." | Não conhecido | Entre em contato com o Microsoft Support.
9024: "O valor variável do ambiente TEMP está vazio." | Não conhecido | Entre em contato com o Microsoft Support.
9025: "PowerShell é corrupto nos VMs convidados." | Não conhecido | Volte a instalar o PowerShell no VM do hóspede e verifique se o PowerShell pode ser executado no VM convidado.
8084: "Incapaz de descobrir aplicações devido a erro da VMware:  <Exception from VMware> " | O aparelho Azure Migrate utiliza APIs VMware para descobrir aplicações. Este problema pode acontecer se uma exceção for lançada pelo vCenter Server enquanto tenta descobrir aplicações. A mensagem de falha do VMware é exibida na mensagem de erro mostrada no portal. | Procure a mensagem na [documentação VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)e siga os passos a corrigir. Se não conseguir corrigir, contacte o suporte da Microsoft.



## <a name="next-steps"></a>Passos seguintes
Configurar um aparelho para [VMware,](how-to-set-up-appliance-vmware.md) [Hiper-V](how-to-set-up-appliance-hyper-v.md)ou [servidores físicos](how-to-set-up-appliance-physical.md).
