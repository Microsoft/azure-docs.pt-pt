---
title: 'Azure AD Connect: Upgrade de uma versão anterior Microsoft Docs'
description: Explica os diferentes métodos para atualizar para o mais recente lançamento do Azure Ative Directory Connect, incluindo uma atualização no local e uma migração de balanço.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65fc0e84582c005c5796ceac86ee28fc46b2e1d8
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094221"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: atualizar de uma versão anterior para a mais recente
Este tópico descreve os diferentes métodos que pode utilizar para atualizar o seu Diretório Ativo Azure (Azure AD) Ligar a instalação à mais recente versão.  Também utiliza os passos na secção [de migração swing](#swing-migration) quando faz uma alteração substancial de configuração.

>[!NOTE]
> É importante que mantenha os seus servidores atuais com as mais recentes versões do Azure AD Connect. Estamos constantemente a fazer upgrades para o AADConnect, e estas atualizações incluem correções para problemas de segurança e bugs, bem como melhorias de serviço, desempenho e escalabilidade. Para ver qual é a versão mais recente, e para saber que mudanças foram feitas entre versões, consulte o histórico da [versão de lançamento](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history)

>[!NOTE]
> Atualmente é suportado para atualizar a partir de qualquer versão do Azure AD Connect para a versão atual. As atualizações in-place da DirSync ou da ADSync não são suportadas e é necessária uma migração de balanço.  Se quiser atualizar a partir de DirSync, consulte [a ferramenta de sincronização AD AD (DirSync)](how-to-dirsync-upgrade-get-started.md) ou a secção [de migração Swing.](#swing-migration)  </br>Na prática, os clientes em versões extremamente antigas podem encontrar problemas não diretamente relacionados com o Azure AD Connect. Servidores que estão em produção há vários anos, normalmente têm vários patches aplicados a eles e nem todos podem ser contabilizados.  Geralmente, os clientes que não atualizaram em 12-18 meses devem considerar uma atualização de balanço, uma vez que esta é a opção mais conservadora e menos arriscada.

Se quiser atualizar a partir de DirSync, consulte [a ferramenta de sincronização AZure AD (DirSync)](how-to-dirsync-upgrade-get-started.md) em vez disso.

Existem algumas estratégias diferentes que pode usar para atualizar o Azure AD Connect.

| Método | Descrição |
| --- | --- |
| [Atualização automática](how-to-connect-install-automatic-upgrade.md) |Este é o método mais fácil para os clientes com uma instalação expressa. |
| [Atualização no local](#in-place-upgrade) |Se tiver um único servidor, pode atualizar a instalação no mesmo servidor. |
| [Migração rotativa](#swing-migration) |Com dois servidores, pode preparar um dos servidores com a nova versão ou configuração e alterar o servidor ativo quando estiver pronto. |

Para obter informações sobre permissões, consulte as [permissões necessárias para uma atualização](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Depois de ter ativado o seu novo servidor AZURE AD Connect para iniciar a sincronização de alterações no AD Azure, não deve voltar a utilizar o DirSync ou o Azure AD Sync. A downgrade do Azure AD Connect para clientes legados, incluindo DirSync e Azure AD Sync, não é suportada e pode levar a questões como a perda de dados em Azure AD.

## <a name="in-place-upgrade"></a>Atualização no local
Uma atualização no local funciona para se mover de Azure AD Sync ou Azure AD Connect. Não funciona para se mudar de DirSync ou para uma solução com o Gestor de Identidade da Vanguarda (FIM) + Azure AD Connector.

Este método é preferido quando se tem um único servidor e menos de 100.000 objetos. Se houver alterações nas regras de sincronização fora de caixa, ocorrem uma importação completa e uma sincronização completa após a atualização. Este método garante que a nova configuração é aplicada a todos os objetos existentes no sistema. Esta execução pode demorar algumas horas, dependendo do número de objetos que estão no âmbito do motor de sincronização. O calendário normal de sincronização delta (que sincroniza a cada 30 minutos por padrão) está suspenso, mas a sincronização da palavra-passe continua. Pode considerar fazer a atualização no local durante um fim de semana. Se não houver alterações na configuração fora de caixa com a nova versão Azure AD Connect, então uma importação/sincronização delta normal começa.  
![Atualização no local](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Se tiver feito alterações às regras de sincronização fora de caixa, então estas regras são rectidas para a configuração predefinida na atualização. Para se certificar de que a sua configuração é mantida entre atualizações, certifique-se de que escoda alterações tal como são descritas nas [melhores práticas para alterar a configuração padrão](how-to-connect-sync-best-practices-changing-default-configuration.md).

Durante a atualização no local, podem ser introduzidas alterações que exigem que atividades específicas de sincronização (incluindo passo de Importação Completa e passo de Sincronização Completa) sejam executadas após a conclusão do upgrade. Para adiar tais atividades, consulte a secção [Como adiar a sincronização completa após a atualização](#how-to-defer-full-synchronization-after-upgrade).

Se estiver a utilizar o Azure AD Connect com um conector não padrão (por exemplo, Conector LDAP Genérico e Conector SQL Genérico), deve atualizar a configuração correspondente do conector no Gestor de Serviço de [Sincronização](./how-to-connect-sync-service-manager-ui-connectors.md) após a atualização no local. Para obter mais informações sobre como refrescar a configuração do conector, consulte a secção de [artigos Connector Version Release History - Troubleshooting](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-version-history#troubleshooting). Se não atualizar a configuração, as etapas de importação e de exportação não funcionarão corretamente para o conector. Receberá o seguinte erro no registo do evento de aplicação com a mensagem *"A versão de montagem na configuração do Conector AAD ("X.X.XXX.X") é mais cedo do que a versão real ("X.X.XXX.X") de "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Migração rotativa
Se tiver uma implementação complexa ou muitos objetos, pode ser impraticável fazer uma atualização no local do sistema ao vivo. Para alguns clientes, este processo pode demorar vários dias -- e durante este tempo, não são processadas alterações delta. Também pode usar este método quando planeia fazer alterações substanciais na sua configuração e pretende experimentá-las antes de serem empurradas para a nuvem.

O método recomendado para estes cenários é utilizar uma migração de balanço. Precisa (pelo menos) de dois servidores-- um servidor ativo e um servidor de paragem. O servidor ativo (apresentado com linhas azuis sólidas na seguinte imagem) é responsável pela carga de produção ativa. O servidor de preparação (apresentado com linhas roxas tracejadas) é preparado com o novo lançamento ou configuração. Quando estiver totalmente pronto, este servidor é feito ativo. O servidor ativo anterior, que agora tem a versão ou configuração antiga instalada, é feito no servidor de staging e é atualizado.

Os dois servidores podem utilizar versões diferentes. Por exemplo, o servidor ativo que planeia desativar pode utilizar o Azure AD Sync, e o novo servidor de staging pode utilizar o Azure AD Connect. Se utilizar a migração de balanço para desenvolver uma nova configuração, é uma boa ideia ter as mesmas versões nos dois servidores.  
![Servidor de encenação](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Alguns clientes preferem ter três ou quatro servidores para este cenário. Quando o servidor de paragem é atualizado, não tem um servidor de reserva para [a recuperação de desastres](how-to-connect-sync-staging-server.md#disaster-recovery). Com três ou quatro servidores, pode preparar um conjunto de servidores primários/standby com a nova versão, que garante que há sempre um servidor de preparação pronto a assumir.

Estes passos também funcionam para passar do Azure AD Sync ou uma solução com o FIM + Azure AD Connector. Estes passos não funcionam para o DirSync, mas o mesmo método de migração de balanço (também chamado de implantação paralela) com passos para o DirSync está na [sincronização do Upgrade Azure Ative Directory (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Use uma migração de balanço para atualizar
1. Se utilizar o Azure AD Connect em ambos os servidores e planeia apenas fazer uma alteração de configuração, certifique-se de que o servidor ativo e o servidor de paragem estão ambos a usar a mesma versão. Isso torna mais fácil comparar diferenças mais tarde. Se estiver a atualizar a partir do Azure AD Sync, então estes servidores têm versões diferentes. Se estiver a atualizar a partir de uma versão mais antiga do Azure AD Connect, é uma boa ideia começar com os dois servidores que estão a usar a mesma versão, mas não é necessário.
2. Se tiver feito uma configuração personalizada e o seu servidor de paragem não o tiver, siga os passos sob [a configuração personalizada do servidor ativo para o servidor de paragem](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Se estiver a atualizar a partir de uma versão anterior do Azure AD Connect, atualize o servidor de preparação para a versão mais recente. Se estiver a mover-se do Azure AD Sync, instale o Azure AD Connect no seu servidor de preparação.
4. Deixe o motor de sincronização executar a importação completa e a sincronização total no seu servidor de preparação.
5. Verifique se a nova configuração não provocou alterações inesperadas utilizando os passos em "Verificar" na [Verificação da configuração de um servidor](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Se algo não for como esperado, corrija-o, execute a importação e sincronização, e verifique os dados até que pareça bom, seguindo os passos.
6. Mude o servidor de preparação para ser o servidor ativo. Este é o passo final "Switch ative server" em [Verificar a configuração de um servidor](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Se estiver a atualizar o Azure AD Connect, atualize o servidor que está agora em modo de preparação para a versão mais recente. Siga os mesmos passos que antes para atualizar os dados e a configuração. Se for atualizado a partir do Azure AD Sync, pode agora desligar e desativar o seu antigo servidor.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Mover uma configuração personalizada do servidor ativo para o servidor de realização
Se tiver escoado alterações de configuração no servidor ativo, tem de se certificar de que as mesmas alterações são aplicadas no servidor de preparação. Para ajudar neste movimento, pode utilizar o documento de [configuração AZURE AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Pode mover as regras de sincronização personalizadas que criou usando o PowerShell. Tens de aplicar outras alterações da mesma forma em ambos os sistemas, e não podes migrar as alterações. O [documento de configuração](https://github.com/Microsoft/AADConnectConfigDocumenter) pode ajudá-lo a comparar os dois sistemas para se certificar de que são idênticos. A ferramenta também pode ajudar a automatizar os passos encontrados nesta secção.

Tem de configurar as seguintes coisas da mesma forma em ambos os servidores:

* Ligação às mesmas florestas
* Qualquer domínio e filtragem da U
* As mesmas funcionalidades opcionais, tais como sincronização de palavras-passe e writeback de palavra-passe

**Mover regras de sincronização personalizadas**  
Para mover regras de sincronização personalizadas, faça o seguinte:

1. Abrir **o Editor de Regras de Sincronização** no seu servidor ativo.
2. Selecione uma regra personalizada. Clique em **Exportar**. Isto traz uma janela do Bloco de Notas. Guarde o ficheiro temporário com uma extensão PS1. Isto faz com que seja um guião PowerShell. Copie o ficheiro PS1 para o servidor de encenação.  
   ![Exportação de regras de sincronização](./media/how-to-upgrade-previous-version/exportrule.png)
3. O Conector GUID é diferente no servidor de paragem e tem de alterá-lo. Para obter o GUID, inicie o **Synchronization Rules Editor**, selecione uma das regras fora da caixa que representam o mesmo sistema conectado, e clique em **Exportação**. Substitua o GUID no seu ficheiro PS1 pelo GUID do servidor de encenação.
4. Num pedido powerShell, execute o ficheiro PS1. Isto cria a regra de sincronização personalizada no servidor de encenação.
5. Repita isto para todas as suas regras personalizadas.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Como adiar a sincronização completa após a atualização
Durante a atualização no local, podem ser introduzidas alterações que exigem que sejam executadas atividades específicas de sincronização (incluindo passo de Importação Completa e etapa de sincronização completa). Por exemplo, as alterações do esquema do conector requerem alterações **completas** do passo de importação e das regras de sincronização fora da caixa requerem que o passo **de sincronização total** seja executado nos conectores afetados. Durante a atualização, o Azure AD Connect determina quais as atividades de sincronização necessárias e regista-as como *substituições*. No ciclo de sincronização seguinte, o programador de sincronização pega nestas sobreposições e executa-as. Uma vez executada com sucesso, é removida.

Pode haver situações em que não queira que estas sobreposições ocorram imediatamente após a atualização. Por exemplo, tem inúmeros objetos sincronizados e gostaria que estes passos de sincronização ocorressem após o horário comercial. Para remover estas sobreposições:

1. Durante a atualização, **desmarque** a opção **Inicie o processo de sincronização quando a configuração estiver concluída**. Isto desativa o programador de sincronização e impede que o ciclo de sincronização ocorra automaticamente antes de as sobreposições serem removidas.

   ![Screenshot que realça o processo de sincronização Iniciar quando a configuração completa a opção que precisa de limpar.](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Após a atualização concluída, execute o seguinte cmdlet para saber quais as sobreposições adicionadas: `Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > As sobreposições são específicas do conector. No exemplo seguinte, o passo full import e full synchronization foram adicionados tanto ao conector AD no local como ao conector AD AZure.

   ![DisableFullSyncAfterUpgrade](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Note as sobreposições existentes que foram adicionadas.
   
4. Para eliminar as sobreposições tanto para a importação completa como para a sincronização completa num conector arbitrário, executar o seguinte cmdlet: `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Para remover as sobreposições em todos os conectores, execute o seguinte script PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Para retomar o programador, executar o seguinte cmdlet: `Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Lembre-se de executar os passos de sincronização necessários o mais rápido possível. Pode executar manualmente estes passos utilizando o Gestor de Serviço de Sincronização ou adicionar as sobreposições utilizando o Set-ADSyncSchedulerConnectorOverride cmdlet.

Para adicionar as sobreposições tanto para a importação completa como para a sincronização completa num conector arbitrário, executar o seguinte cmdlet:  `Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Resolução de problemas
A secção seguinte contém resolução de problemas e informações que pode utilizar se encontrar um problema que adure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Erro do conector Azure Ative Directory em falta durante a atualização AD AD do Azure

Quando atualizar o Azure AD Connect a partir de uma versão anterior, poderá acertar na sequência de um erro no início da atualização 

![Erro](./media/how-to-upgrade-previous-version/error1.png)

Este erro ocorre porque o conector Azure Ative Directory com identificador, b891884f-051e-4a83-95af-2544101c9083, não existe na configuração atual do Azure AD Connect. Para verificar se este é o caso, abra uma janela PowerShell, corra Cmdlet `Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

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

O Comandante PowerShell informa o erro **que o MA especificado não foi encontrado**.

A razão pela qual isto ocorre é porque a configuração atual do Azure AD Connect não é suportada para atualização. 

Se pretender instalar uma versão mais recente do Azure AD Connect: feche o assistente AZure AD Connect, desinstale o Azure AD Connect existente e realize uma instalação limpa do mais recente Azure AD Connect.



## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a integração das suas identidades no local com o Azure Ative Directory](whatis-hybrid-identity.md).
