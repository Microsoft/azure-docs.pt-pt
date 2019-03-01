---
title: 'Azure AD Connect: Atualização de uma versão anterior | Documentos da Microsoft'
description: Explica os diferentes métodos para atualizar para a versão mais recente do Azure Active Directory Connect, incluindo uma atualização no local e uma migração rotativa.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 07/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa21b1054fa6860a8acc5d6971f75e1d74c889f7
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193760"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: Atualizar de uma versão anterior para a versão mais recente
Este tópico descreve os diferentes métodos que pode utilizar para atualizar sua instalação do Azure Active Directory (Azure AD) Connect para a versão mais recente. Recomendamos que mantenha-se atualizado com as versões do Azure AD Connect. Também é usar os passos a [migração rotativa](#swing-migration) secção quando fizer uma alteração de configuração substancial.

Se quiser atualizar do DirSync, consulte [atualizar da ferramenta de sincronização do Azure AD (DirSync) do](how-to-dirsync-upgrade-get-started.md) em vez disso.

Existem algumas estratégias diferentes que pode utilizar para atualizar o Azure AD Connect.

| Método | Descrição |
| --- | --- |
| [Atualização automática](how-to-connect-install-automatic-upgrade.md) |Este é o método mais fácil para os clientes com uma instalação rápida. |
| [Atualização no local](#in-place-upgrade) |Se tiver um único servidor, pode atualizar o instalação no local no mesmo servidor. |
| [Migração rotativa](#swing-migration) |Com dois servidores, pode preparar um dos servidores com a nova versão ou a configuração e alterar o servidor de Active Directory quando estiver pronto. |

Para informações de permissões, consulte a [as permissões necessárias para uma atualização](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Depois de ativar seu novo servidor do Azure AD Connect iniciar a sincronização de alterações para o Azure AD, é necessário não reverter para o DirSync ou o Azure AD Sync. A mudança do Azure AD Connect para clientes legados, incluindo o DirSync e Azure AD Sync, não é suportada e pode causar problemas, tais como a perda de dados no Azure AD.

## <a name="in-place-upgrade"></a>Atualização no local
Uma atualização in-loco funciona para a passagem do Azure AD Sync ou do Azure AD Connect. Ele não funciona para a passagem do DirSync ou para uma solução com o Forefront Identity Manager (FIM) + conector Azure AD.

Este método é preferível quando tiver um único servidor e menos de cerca de 100.000 objetos. Se existirem quaisquer alterações às regras de sincronização de out-of-box, uma importação completa e uma sincronização completa ocorrerem após a atualização. Esse método garante que a nova configuração é aplicada a todos os objetos existentes no sistema. Esta execução poderá demorar algumas horas, dependendo do número de objetos que estão no âmbito do motor de sincronização. O agendador de sincronização normal delta (que sincroniza a cada 30 minutos por predefinição) está suspenso, mas continua a sincronização de palavra-passe. Pode optar por fazer a atualização no local durante um final de semana. Se existirem que sem alterações à configuração de out-of-box com o Azure AD Connect com a nova versão, em seguida, uma importação/sincronização normal delta inicia em vez disso.  
![Atualização no local](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Se fez alterações para as regras de sincronização de out-of-box, em seguida, estas regras estiverem definidas volta à configuração padrão a actualização. Para certificar-se de que a configuração é mantida entre as atualizações, certifique-se de que faça alterações conforme estiver descritos [melhores práticas para alterar a configuração predefinida](how-to-connect-sync-best-practices-changing-default-configuration.md).

Durante a atualização no local, pode haver alterações introduzidas que necessitam de atividades de sincronização específica (incluindo o passo de importação completa e o passo de sincronização completa) a ser executado após a conclusão da atualização. Para diferir tais atividades, consulte a secção [como diferir a sincronização completa após a atualização](#how-to-defer-full-synchronization-after-upgrade).

Se estiver a utilizar o Azure AD Connect com o conector não padrão (por exemplo, o conector LDAP genérico e o conector do SQL genérico), tem de atualizar a configuração do conector correspondente no [Synchronization Service Manager](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) após a atualização no local. Para obter detalhes sobre como atualizar a configuração do conector, consulte a secção do artigo [histórico de versões do conector - solução de problemas](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Se não atualizar a configuração, a importação e exportação executar passos não funcionará corretamente para o conector. Receberá o erro seguinte no log de eventos do aplicativo com a mensagem *"versão da assemblagem na configuração do conector AAD ("X.X.XXX. X") é anterior à versão real ("X.X.XXX. X") de"C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Migração rotativa
Se tiver uma implantação complexa ou muitos objetos, poderá ser impraticável para fazer uma atualização no local no sistema ao vivo. Para alguns clientes, este processo poderá demorar vários dias, e durante este período, sem alterações de delta são processadas. Também pode utilizar este método quando pretender efetuar alterações substanciais à sua configuração e quer experimentá-las antes de estiver enviada por push para a cloud.

O método recomendado para esses cenários é usar uma migração rotativa. Precisa (pelo menos) dois servidores--um servidor ativo e um servidor de preparação. O servidor Active Directory (mostrado com linhas azuis sólidas na imagem seguinte) é responsável pela carga de produção do Active Directory. O servidor de preparação (mostrado com linhas tracejadas de roxa) é preparado com a nova versão ou configuração. Quando está totalmente pronto, este servidor é feito Active Directory. O Active Directory do servidor anterior, que agora tem a versão antiga ou de configuração instalada, é feito no servidor de preparação e é atualizado.

Os dois servidores, podem utilizar uma versão diferente. Por exemplo, o servidor Active Directory que planeia desativar pode utilizar o Azure AD Sync, e o novo servidor de teste, pode utilizar o Azure AD Connect. Se utilizar a migração rotativa para desenvolver uma nova configuração, é uma boa idéia ter as mesmas versões em dois servidores.  
![Servidor de teste](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Alguns clientes preferem ter três ou quatro servidores para este cenário. Quando o servidor de preparação é atualizado, não tiver um servidor de cópia de segurança para [recuperação após desastre](how-to-connect-sync-staging-server.md#disaster-recovery). Com três ou quatro servidores, pode preparar um conjunto de servidores primário/em espera com a nova versão, o que garante que sempre há um servidor de teste está pronto para assumir o controlo.

Estes passos também funcionam para mover do Azure AD Sync ou uma solução com o FIM + conector Azure AD. Estes passos não funcionam para o DirSync, mas o mesmo swing método de migração (também chamado de implementação paralela) com os passos para o DirSync está no [sincronização de atualizar o Azure Active Directory (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Utilize uma migração rotativa para atualizar
1. Se utilizar o Azure AD Connect em ambos os servidores e planeie efetuar apenas uma configuração alterar, certifique-se de que o servidor Active Directory e o servidor de preparação ambos utilizam a mesma versão. Que torna mais fácil de comparar as diferenças mais tarde. Se estiver a atualizar a partir do Azure AD Sync, em seguida, estes servidores têm versões diferentes. Se estiver a atualizar a partir de uma versão mais antiga do Azure AD Connect, é uma boa idéia começar com os dois servidores que estão a utilizar a mesma versão, mas não seja necessário.
2. Se fez uma configuração personalizada e o servidor de preparação não o tiver, siga os passos em [mover uma configuração personalizada do servidor ativo para o servidor de preparação](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Se estiver a atualizar a partir de uma versão anterior do Azure AD Connect, atualize o servidor de preparação para a versão mais recente. Se estiver a mover a partir do Azure AD Sync, em seguida, instale o Azure AD Connect no seu servidor de preparação.
4. Permitir que o motor de sincronização executar a importação completa e a sincronização completa no seu servidor de preparação.
5. Certifique-se que a nova configuração não causa quaisquer alterações inesperadas, utilizando os passos em "Verificar" na [verificar a configuração de um servidor](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Se algo não estiver conforme esperado, corrigi-lo, execute a importação e sincronização e verifique se os dados até que isso parece bom, ao seguir os passos.
6. Mude o servidor de preparação para o servidor Active Directory. Esta é a etapa final "Servidor de Active Directory de comutador" na [verificar a configuração de um servidor](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Se estiver a atualizar o Azure AD Connect, atualize o servidor que está agora em modo de teste para a versão mais recente. Siga os mesmos passos para obter os dados e a configuração atualizada. Se atualizar a partir do Azure AD Sync, pode agora desligar e desativar seu servidor antigo.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Mover uma configuração personalizada do servidor ativo para o servidor de preparação
Se fez alterações de configuração para o servidor do Active Directory, terá de certificar-se de que as mesmas alterações são aplicadas para o servidor de preparação. Para ajudar com esta mudança, pode utilizar o [documenter de configuração do Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Pode mover que a sincronização personalizada de regras que criou com o PowerShell. Tem de aplicar outras alterações da mesma forma nos dois sistemas, e não é possível migrar as alterações. O [documenter configuração](https://github.com/Microsoft/AADConnectConfigDocumenter) pode ajudá-lo a comparar os dois sistemas para se certificar de que eles são idênticos. A ferramenta também pode ajudar em automatizar as etapas nesta seção.

Tem de configurar os seguintes procedimentos da mesma forma em ambos os servidores:

* Ligação para as mesmo florestas
* Qualquer domínio e, em seguida, a filtragem de UO
* As mesmas funcionalidades opcionais, como a sincronização de palavra-passe e a repetição de escrita de palavra-passe

**Mover as regras de sincronização personalizados**  
Para mover as regras de sincronização personalizado, faça o seguinte:

1. Open **Editor de regras de sincronização** no seu servidor ativo.
2. Selecione uma regra personalizada. Clique em **exportar**. Isso exibirá uma janela do bloco de notas. Guarde o ficheiro temporário com uma extensão de PS1. Desta forma, um script do PowerShell. Copie o arquivo PS1 para o servidor de preparação.  
   ![Exportação de regra de sincronização](./media/how-to-upgrade-previous-version/exportrule.png)
3. O GUID de conector é diferente no servidor de preparação e deve alterá-la. Para obter o GUID, inicie **Editor de regras de sincronização**, selecione uma das regras de out-of-box que representam o mesmo sistema ligado e clique em **exportar**. Substitua o GUID em seu arquivo PS1 o GUID do servidor de teste.
4. Na linha de comandos do PowerShell, execute o ficheiro PS1. Esta ação cria a regra de sincronização personalizado no servidor de preparação.
5. Repetir isso para todas as suas regras personalizadas.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Como diferir a sincronização completa depois da atualização
Durante a atualização no local, pode haver alterações introduzidas que necessitam de atividades de sincronização específica (incluindo o passo de importação completa e o passo de sincronização completa) a ser executado. Por exemplo, exigem alterações de esquema do conector **importação completa** requerem alterações de regra de sincronização do passo e out-of-box **completa sincronização** passo a ser executado em conectores afetados. Durante a atualização, o Azure AD Connect determina as atividades de sincronização são necessárias e regista-los como *substitui*. No ciclo de sincronização seguinte, o agendador de sincronização são recolhidos por estas substituições e os executa. Depois de uma substituição é executada com êxito, este é removido.

Poderão existir situações onde não pretenda estas substituições para ter lugar imediatamente após a atualização. Por exemplo, tem vários objetos sincronizados e gostaria de ter estes passos de sincronização para ocorrer após o horário comercial. Para remover estas substituições:

1. Durante a actualização **desmarque** a opção **iniciar o processo de sincronização quando tiver concluído a configuração**. Isso desativa o agendador de sincronização e impede o ciclo de sincronização ocorra automaticamente antes das substituições são removidas.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Depois de concluída a atualização, execute o cmdlet seguinte para saber quais substituições foram adicionadas: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > As substituições são específicas do conector. No exemplo a seguir, o passo de importação completa e o passo de sincronização completa foram adicionados para no local conector AD e o conector do Azure AD.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Aponte as substituições existentes que foram adicionadas.
   
4. Para remover as substituições para importação completa e uma sincronização completa num conector arbitrário, execute o seguinte cmdlet: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Para remover as substituições em todos os conectores, execute o seguinte script do PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Para retomar o scheduler, execute o seguinte cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Lembre-se executar os passos de sincronização necessário o quanto. Manualmente pode executar estes passos com o Synchronization Service Manager ou adicionar que as substituições fazer uma cópia com o cmdlet Set-ADSyncSchedulerConnectorOverride.

Para adicionar as substituições para importação completa e sincronização completa de um conector arbitrário, execute o seguinte cmdlet:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Resolução de problemas
A secção seguinte contém informações que pode utilizar se ocorrer um problema ao atualizar o Azure AD Connect e resolução de problemas.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Atualizar o erro do Azure Active Directory connector em falta durante o Azure AD Connect

Ao atualizar o Azure AD Connect de uma versão anterior, o que pode usar o erro no início da atualização do seguinte 

![Erro](./media/how-to-upgrade-previous-version/error1.png)

Este erro ocorre porque o conector Azure Active Directory com o identificador, b891884f-051e-4a83-95af - 2544101c 9083, não existe na configuração atual do Azure AD Connect. Para verificar se que esse for o caso, abra uma janela do PowerShell, execute o Cmdlet `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

```
PS C:\> Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
Get-ADSyncConnector : Operation failed because the specified MA could not be found.
At line:1 char:1
+ Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ReadError: (Microsoft.Ident...ConnectorCmdlet:GetADSyncConnectorCmdlet) [Get-ADSyncConne
   ctor], ConnectorNotFoundException
    + FullyQualifiedErrorId : Operation failed because the specified MA could not be found.,Microsoft.IdentityManageme
   nt.PowerShell.Cmdlet.GetADSyncConnectorCmdlet

```

O Cmdlet do PowerShell reporta o erro **não foi possível encontrar o MA especificado**.

O motivo que isso ocorre é que a configuração atual do Azure AD Connect não é suportada para atualização. 

Se pretender instalar uma versão mais recente do Azure AD Connect: fechar o Assistente do Azure AD Connect, desinstale o existente do Azure AD Connect e executar uma instalação limpa do Azure AD Connect com a mais recente.



## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).
