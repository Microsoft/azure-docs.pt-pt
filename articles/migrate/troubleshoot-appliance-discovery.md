---
title: Solucionar problemas de implantação e descoberta do dispositivo de migração do Azure
description: Obtenha ajuda com a implantação do dispositivo de migrações para Azure e a descoberta de máquinas.
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: 1dbbe04818e75e41944a1c0fe54f53849cf71983
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725861"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>Solucionar problemas do dispositivo e da descoberta de migrações para Azure

Este artigo ajuda você a solucionar problemas ao implantar o dispositivo de [migrações para Azure](migrate-services-overview.md) e usar o dispositivo para descobrir computadores locais.


## <a name="whats-supported"></a>O que é suportado?

[Examine](migrate-appliance.md) os requisitos de suporte do dispositivo.


## <a name="invalid-ovf-manifest-entry"></a>"Entrada de manifesto OVF inválida"

Se você receber o erro "o arquivo de manifesto fornecido é inválido: entrada de manifesto OVF inválida", faça o seguinte:

1. Verifique se o arquivo OVA do dispositivo de migração do Azure foi baixado corretamente verificando seu valor de hash. [Saiba mais](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware). Se o valor de hash não corresponder, baixe o arquivo OVA novamente e repita a implantação.
2. Se a implantação ainda falhar e você estiver usando o VMware vSphere cliente para implantar o arquivo OVF, tente implantá-lo por meio do cliente Web vSphere. Se a implantação ainda falhar, tente usar um navegador da Web diferente.
3. Se você estiver usando o cliente Web vSphere e tentando implantá-lo no vCenter Server 6,5 ou 6,7, tente implantar o OVA diretamente no host ESXi:
   - Conecte-se diretamente ao host ESXi (em vez de vCenter Server) com o cliente Web (*endereço IP do host*do https://< >/UI).
   - Em **início** > **inventário**, selecione **arquivo** > **implantar o modelo OVF**. Navegue até o OVA e conclua a implantação.
4. Se a implantação ainda falhar, entre em contato com o suporte para migrações para Azure.

## <a name="cant-connect-to-the-internet"></a>Não é possível conectar à Internet

Isso pode acontecer se a máquina do dispositivo estiver atrás de um proxy.

- Certifique-se de fornecer as credenciais de autorização se o proxy precisar delas.
- Se você estiver usando um proxy de firewall baseado em URL para controlar a conectividade de saída, adicione essas URLs a uma lista de permissões:

    - [URLs para avaliação do VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#assessment-url-access-requirements)
    - [URLs para avaliação do Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#assessment-appliance-url-access)
    - [URLs para migração sem agente do VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-migration-url-access-requirements)
    - [URLS para migração baseada em agente do VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#replication-appliance-url-access)
    - [URLs para migração do Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#migration-hyper-v-host-url-access)

- Se você estiver usando um proxy de interceptação para se conectar à Internet, importe o certificado de proxy para a VM do dispositivo usando [estas etapas](https://docs.microsoft.com/azure/migrate/concepts-collector).

##  <a name="datetime-synchronization-error"></a>Erro de sincronização de data/hora

Um erro sobre a sincronização de data e hora (802) indica que o relógio do servidor pode estar fora de sincronização com a hora atual em mais de cinco minutos. Altere a hora do relógio na VM do coletor para corresponder à hora atual:

1. Abra um prompt de comando de administrador na VM.
2. Para verificar o fuso horário, execute **w32tm/TZ**.
3. Para sincronizar o tempo, execute **w32tm/resync**.


## <a name="unabletoconnecttoserver"></a>UnableToConnectToServer

Se você receber esse erro de conexão, talvez não consiga se conectar ao vCenter Server *ServerName*. com: 9443. Os detalhes do erro indicam que não há nenhum ponto de extremidade ouvindo em https://*ServerName*. com: 9443/SDK que pode aceitar a mensagem.

- Verifique se você está executando a versão mais recente do dispositivo. Se você não tiver, atualize o dispositivo para a [versão mais recente](https://docs.microsoft.com/azure/migrate/concepts-collector).
- Se o problema ainda ocorrer na versão mais recente, o dispositivo poderá não conseguir resolver o nome de vCenter Server especificado ou a porta especificada poderá estar errada. Por padrão, se a porta não for especificada, o coletor tentará se conectar ao número da porta 443.

    1. Execute o ping *ServerName*. com do dispositivo.
    2. Se a etapa 1 falhar, tente se conectar ao servidor do vCenter usando o endereço IP.
    3. Identifique o número da porta correto para se conectar ao vCenter Server.
    4. Verifique se vCenter Server está em execução.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>Erro 60052/60039: o dispositivo pode não estar registrado

- O erro 60052, "o dispositivo pode não estar registrado com êxito no projeto de migrações para Azure" ocorrerá se a conta do Azure usada para registrar o dispositivo não tiver permissões suficientes.
    - Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos permissões de colaborador na assinatura.
    - [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as funções e permissões necessárias do Azure.
- O erro 60039, "o dispositivo pode não estar registrado com êxito no projeto de migrações para Azure" pode ocorrer se o registro falhar porque o projeto de migrações para Azure usado para registrar o dispositivo não foi encontrado.
    - No portal do Azure e verifique se o projeto existe no grupo de recursos.
    - Se o projeto não existir, crie um novo projeto de migrações para Azure em seu grupo de recursos e registre o dispositivo novamente. [Saiba como](https://docs.microsoft.com/azure/migrate/how-to-add-tool-first-time#create-a-project-and-add-a-tool) criar um novo projeto.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>Erro 60030/60031: falha na operação de gerenciamento de Key Vault

Se você receber o erro 60030 ou 60031, "falha em uma operação de gerenciamento de Azure Key Vault", faça o seguinte:
- Verifique se a conta de usuário do Azure usada para registrar o dispositivo tem pelo menos permissões de colaborador na assinatura.
- Verifique se a conta tem acesso ao cofre de chaves especificado na mensagem de erro e repita a operação.
- Se o problema persistir, contacte o suporte da Microsoft.
- [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as funções e permissões do Azure necessárias.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>Erro 60028: não foi possível iniciar a descoberta

Erro 60028: "não foi possível iniciar a descoberta devido a um erro. A operação falhou para a lista especificada de hosts ou clusters "indica que a descoberta não pôde ser iniciada nos hosts listados no erro devido a um problema no acesso ou na recuperação de informações da VM. O restante dos hosts foi adicionado com êxito.

- Adicione os hosts listados no erro novamente, usando a opção **Adicionar host** .
- Se houver um erro de validação, examine as diretrizes de correção para corrigir os erros e tente a opção **salvar e iniciar descoberta** novamente.

## <a name="error-60025-azure-ad-operation-failed"></a>Erro 60025: falha na operação do Azure AD 
Erro 60025: "falha em uma operação do Azure AD. O erro ocorreu ao criar ou atualizar o aplicativo do Azure AD "ocorre quando a conta de usuário do Azure usada para iniciar a descoberta é diferente da conta usada para registrar o dispositivo. Efetue uma das seguintes ações:

- Verifique se a conta de usuário que está iniciando a descoberta é a mesma usada para registrar o dispositivo.
- Forneça Azure Active Directory permissões de acesso do aplicativo à conta de usuário para a qual a operação de descoberta está falhando.
- Exclua o grupo de recursos criado anteriormente para o projeto de migrações para Azure. Crie outro grupo de recursos para iniciar novamente.
- [Saiba mais](https://docs.microsoft.com/azure/migrate/migrate-appliance#appliance-deployment-requirements) sobre as permissões de aplicativo Azure Active Directory.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>Erro 50004: não é possível conectar-se ao host ou cluster

Erro 50004: "não é possível se conectar a um host ou cluster porque o nome do servidor não pode ser resolvido. Código de erro WinRM: 0x803381B9 "poderá ocorrer se o serviço DNS do Azure para o dispositivo não puder resolver o cluster ou o nome do host fornecido.

- Se você vir esse erro no cluster, o FQDN do cluster.
- Você também pode ver esse erro para hosts em um cluster. Isso indica que o dispositivo pode se conectar ao cluster, mas o cluster retorna nomes de host que não são FQDNs. Para resolver esse erro, atualize o arquivo de hosts no dispositivo adicionando um mapeamento do endereço IP e nomes de host:
    1. Abra o bloco de notas como administrador.
    2. Abra o arquivo C:\Windows\System32\Drivers\etc\hosts.
    3. Adicione o endereço IP e o nome do host em uma linha. Repita para cada host ou cluster em que você vê esse erro.
    4. Guarde e feche o ficheiro de anfitriões.
    5. Verifique se o dispositivo pode se conectar aos hosts, usando o aplicativo de gerenciamento de dispositivo. Após 30 minutos, você deverá ver as informações mais recentes para esses hosts na portal do Azure.

## <a name="discovered-vms-not-in-portal"></a>VMs descobertas não estão no portal

Se o estado da descoberta for "descoberta em andamento", mas ainda não estiver vendo as VMs no portal, aguarde alguns minutos:
- Leva cerca de 15 minutos para uma VM VMware.
- Leva cerca de dois minutos para cada host adicionado para a descoberta de VM do Hyper-V.

Se você aguardar e o estado não for alterado, selecione **Atualizar** na guia **servidores** . Isso deve mostrar a contagem dos servidores descobertos em migrações para Azure: avaliação de servidor e migrações para Azure: migração de servidor.

Se isso não funcionar e você estiver descobrindo servidores VMware:

- Verifique se a conta do vCenter especificada tem permissões definidas corretamente, com acesso a pelo menos uma VM.
- As migrações para Azure não poderão descobrir VMs do VMware se a conta do vCenter tiver acesso concedido no nível da pasta da VM do vCenter. [Saiba mais](tutorial-assess-vmware.md#set-the-scope-of-discovery) sobre a descoberta de escopo.

## <a name="vm-data-not-in-portal"></a>Dados da VM não estão no portal

Se as VMs descobertas não aparecerem no portal, aguarde alguns minutos. Leva até 30 minutos para que os dados descobertos apareçam no Portal. Se não houver dados após 30 minutos, tente atualizar, da seguinte maneira

1. Em **servidores** > **avaliação do servidor de migrações para Azure**, selecione **visão geral**.
2. Em **gerenciar**, selecione **integridade do agente**.
3. Selecione **Atualizar agente**.
4. Aguarde a conclusão da operação de atualização. Agora você deve ver informações atualizadas.

## <a name="deleted-vms-appear-in-portal"></a>VMs excluídas aparecem no portal

Se você excluir VMs e elas ainda aparecerem no portal, aguarde 30 minutos. Se eles ainda aparecerem, atualize conforme descrito acima.

## <a name="common-app-discovery-errors"></a>Erros comuns do App Discovery

As migrações para Azure dão suporte à descoberta de aplicativos, funções e recursos usando migrações para Azure: avaliação do servidor. Atualmente, o app Discovery tem suporte apenas para VMware. [Saiba mais](how-to-discover-applications.md) sobre os requisitos e as etapas para configurar a descoberta de aplicativos.

Erros típicos de descoberta de aplicativo são resumidos na tabela.

**Error** | **Motivo** | **Ação**
--- | --- | --- | ---
10000: "não é possível descobrir os aplicativos instalados no servidor". | Isso pode ocorrer se o sistema operacional do computador não for Windows ou Linux. | Use a descoberta de aplicativos somente para Windows/Linux.
10001: "não é possível recuperar os aplicativos instalados no servidor". | Erro interno-alguns arquivos ausentes no dispositivo. | Contacte o Suporte da Microsoft.
10002: "não é possível recuperar os aplicativos instalados no servidor". | O agente de descoberta no dispositivo pode não estar funcionando corretamente. | Se o problema não for resolvido em até 24 horas, entre em contato com o suporte.
10003 "não foi possível recuperar os aplicativos instalados no servidor". | O agente de descoberta no dispositivo pode não estar funcionando corretamente. | Se o problema não for resolvido em até 24 horas, entre em contato com o suporte.
10004: "não é possível descobrir os aplicativos instalados para < computadores > Windows/Linux". |  As credenciais para acessar < máquinas > Windows/Linux não foram fornecidas no dispositivo.| Adicione uma credencial ao dispositivo que tem acesso ao < computadores > Windows/Linux.
10005: "não é possível acessar o servidor local". | As credenciais de acesso podem estar erradas. | Atualize as credenciais do dispositivo Verifique se você pode acessar o computador relevante com eles. 
10006: "não é possível acessar o servidor local". | Isso pode ocorrer se o sistema operacional do computador não for Windows ou Linux.|  Use a descoberta de aplicativos somente para Windows/Linux.
9000/9001/9002: "não é possível descobrir os aplicativos instalados no servidor". | As ferramentas do VMware podem não estar instaladas ou corrompidas. | Instale/reinstale as ferramentas do VMware no computador relevante e verifique se ela está em execução.
9003: não é possível descobrir os aplicativos instalados no servidor ". | Isso pode ocorrer se o sistema operacional do computador não for Windows ou Linux. | Use a descoberta de aplicativos somente para Windows/Linux.
9004: "não é possível descobrir os aplicativos instalados no servidor". | Isso pode acontecer se a VM estiver desligada. | Para descoberta, verifique se a VM está ativada.
9005: "não é possível descobrir os aplicativos instalados na VM. | Isso pode ocorrer se o sistema operacional do computador não for Windows ou Linux. | Use a descoberta de aplicativos somente para Windows/Linux.
9006/9007: "não é possível recuperar os aplicativos instalados no servidor". | O agente de descoberta no dispositivo pode não estar funcionando corretamente. | Se o problema não for resolvido em até 24 horas, entre em contato com o suporte.
9008: "não é possível recuperar os aplicativos instalados no servidor". | Pode ser um erro interno.  | Tf o problema não é resolvido em até 24 horas, entre em contato com o suporte.
9009: "não é possível recuperar os aplicativos instalados no servidor". | Pode ocorrer se as configurações de UAC (controle de conta de usuário) do Windows no servidor forem restritivas e impedir a descoberta de aplicativos instalados. | Procure configurações de ' controle de conta de usuário ' no servidor e defina a configuração do UAC no servidor para um dos dois níveis inferiores.
9010: "não é possível recuperar os aplicativos instalados no servidor". | Pode ser um erro interno.  | Tf o problema não é resolvido em até 24 horas, entre em contato com o suporte.
8084: "não é possível descobrir aplicativos devido ao erro do VMware: <Exception from VMware>" | O dispositivo de migrações para Azure usa APIs do VMware para descobrir aplicativos. Esse problema pode ocorrer se uma exceção for lançada pelo vCenter Server ao tentar descobrir aplicativos. A mensagem de falha do VMware é exibida na mensagem de erro mostrada no Portal. | Pesquise a mensagem na documentação do [VMware](https://pubs.vmware.com/vsphere-51/topic/com.vmware.wssdk.apiref.doc/index-faults.html)e siga as etapas para corrigir. Se você não puder corrigir, entre em contato com o suporte da Microsoft.




## <a name="next-steps"></a>Passos seguintes
Configure um dispositivo para [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md)ou [servidores físicos](how-to-set-up-appliance-physical.md).
