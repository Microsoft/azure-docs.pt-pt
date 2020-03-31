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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 04/08/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a3e7373a8b0354a3d08debf944f2f77f1609382
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347753"
---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: atualizar de uma versão anterior para a mais recente
Este tópico descreve os diferentes métodos que pode utilizar para atualizar a instalação de ligação do seu Azure Ative Directory (Azure AD) à mais recente versão. Recomendamos que se mantenha atual com os lançamentos do Azure AD Connect. Também utiliza os passos na secção de [migração Swing](#swing-migration) quando faz uma alteração substancial de configuração.

>[!NOTE]
> Atualmente é suportado para atualizar a partir de qualquer versão do Azure AD Connect para a versão atual. As atualizações em vigor do DirSync ou do ADSync não são suportadas e é necessária uma migração de balanço.  Se quiser fazer upgrade a partir do DirSync, consulte o Upgrade da ferramenta de [sincronização Azure AD (DirSync)](how-to-dirsync-upgrade-get-started.md) ou da secção [de migração Swing.](#swing-migration)  </br>Na prática, os clientes em versões extremamente antigas podem encontrar problemas não diretamente relacionados com o Azure AD Connect. Servidores que estão em produção há vários anos, normalmente têm vários patches aplicados a eles e nem todos estes podem ser contabilizados.  Geralmente, os clientes que não atualizaram em 12-18 meses devem considerar uma atualização de balanço, uma vez que esta é a opção mais conservadora e menos arriscada.

Se quiser fazer upgrade a partir do DirSync, consulte o Upgrade da ferramenta de [sincronização Azure AD (DirSync).](how-to-dirsync-upgrade-get-started.md)

Existem algumas estratégias diferentes que pode usar para atualizar o Azure AD Connect.

| Método | Descrição |
| --- | --- |
| [Atualização automática](how-to-connect-install-automatic-upgrade.md) |Este é o método mais fácil para os clientes com uma instalação expressa. |
| [Atualização no local](#in-place-upgrade) |Se tiver um único servidor, pode atualizar a instalação no mesmo servidor. |
| [Migração rotativa](#swing-migration) |Com dois servidores, pode preparar um dos servidores com o novo lançamento ou configuração e alterar o servidor ativo quando estiver pronto. |

Para obter informações sobre permissões, consulte as [permissões necessárias para uma atualização](reference-connect-accounts-permissions.md#upgrade).

> [!NOTE]
> Depois de ter ativado o seu novo servidor Azure AD Connect para começar a sincronizar alterações no Azure AD, não deve voltar a utilizar o DirSync ou o Azure AD Sync. A degradação do Azure AD Connect para clientes legados, incluindo o DirSync e o Azure AD Sync, não é suportada e pode levar a problemas como a perda de dados em Azure AD.

## <a name="in-place-upgrade"></a>Atualização no local
Uma atualização em vigor funciona para a mudança de Azure AD Sync ou Azure AD Connect. Não funciona para a mudança do DirSync ou para uma solução com o Gestor de Identidade da Vanguarda (FIM) + O Conector AD Azure.

Este método é preferido quando se tem um único servidor e menos de 100.000 objetos. Se houver alterações às regras de sincronização fora da caixa, ocorre uma importação completa e uma sincronização completa após a atualização. Este método garante que a nova configuração seja aplicada a todos os objetos existentes no sistema. Esta corrida pode demorar algumas horas, dependendo do número de objetos que estão no âmbito do motor de sincronização. O programador normal de sincronização delta (que sincroniza a cada 30 minutos por padrão) está suspenso, mas a sincronização da palavra-passe continua. Talvez considere fazer o upgrade durante um fim de semana. Se não houver alterações na configuração fora da caixa com a nova versão Azure AD Connect, então um delta normal importa/sincronização começa.  
![Atualização no local](./media/how-to-upgrade-previous-version/inplaceupgrade.png)

Se fez alterações nas regras de sincronização fora da caixa, então estas regras são definidas para a configuração padrão na atualização. Para se certificar de que a sua configuração é mantida entre atualizações, certifique-se de que faz alterações tal como são descritas nas [melhores práticas para alterar a configuração predefinida](how-to-connect-sync-best-practices-changing-default-configuration.md).

Durante a atualização no local, pode haver alterações que requerem atividades específicas de sincronização (incluindo o passo de importação completa e o passo de sincronização completa) a serem executadas após a atualização concluída. Para adiar tais atividades, consulte a secção [Como adiar a sincronização completa após](#how-to-defer-full-synchronization-after-upgrade)a atualização .

Se estiver a utilizar o Azure AD Connect com um conector não normalizado (por exemplo, conector Genérico LDAP e Conector Genérico SQL), tem de atualizar a configuração correspondente do conector no Gestor de Serviços de [Sincronização](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-service-manager-ui-connectors) após a atualização no local. Para mais detalhes sobre como atualizar a configuração do conector, consulte a secção de artigos [Connector Version Release History - Troubleshooting](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-connector-version-history#troubleshooting). Se não atualizar a configuração, os passos de importação e exportação não funcionarão corretamente para o conector. Receberá o seguinte erro no registo do evento de aplicação com a mensagem *"Versão de montagem na configuração do Conector AAD ("X.X.XXX. X") é mais cedo do que a versão real ("X.X.XXX. X") de "C:\Program Files\Microsoft Azure AD Sync\Extensions\Microsoft.IAM.Connector.GenericLdap.dll".*

## <a name="swing-migration"></a>Migração rotativa
Se tiver uma implantação complexa ou muitos objetos, pode ser impraticável fazer uma atualização no local no sistema ao vivo. Para alguns clientes, este processo pode demorar vários dias - e durante este tempo, não são processadas alterações delta. Também pode usar este método quando planeia fazer alterações substanciais na sua configuração e quer experimentá-las antes de serem empurradas para a nuvem.

O método recomendado para estes cenários é utilizar uma migração de balanço. Precisa (pelo menos) de dois servidores- um servidor ativo e um servidor de encenação. O servidor ativo (mostrado com linhas azuis sólidas na imagem seguinte) é responsável pela carga de produção ativa. O servidor de encenação (mostrado com linhas roxas tracejadas) é preparado com o novo lançamento ou configuração. Quando está totalmente pronto, este servidor é ativo. O servidor ativo anterior, que agora tem a versão antiga ou configuração instalada, é fabricado no servidor de encenação e é atualizado.

Os dois servidores podem usar versões diferentes. Por exemplo, o servidor ativo que planeia desativar pode utilizar o Azure AD Sync, e o novo servidor de encenação pode utilizar o Azure AD Connect. Se utilizar a migração de swing para desenvolver uma nova configuração, é uma boa ideia ter as mesmas versões nos dois servidores.  
![Servidor de encenação](./media/how-to-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Alguns clientes preferem ter três ou quatro servidores para este cenário. Quando o servidor de encenação é atualizado, não tem um servidor de reserva para [recuperação](how-to-connect-sync-staging-server.md#disaster-recovery)de desastres. Com três ou quatro servidores, pode preparar um conjunto de servidores primários/de standby com a nova versão, que garante que há sempre um servidor de encenação que está pronto para assumir.

Estes passos também funcionam para se mover do Azure AD Sync ou uma solução com o Conector AD FIM + Azure. Estes passos não funcionam para o DirSync, mas o mesmo método de migração do balanço (também chamado de implantação paralela) com passos para o DirSync está em [upgrade Azure Ative Directory sync (DirSync)](how-to-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Use uma migração de balanço para atualizar
1. Se utilizar o Azure AD Connect em ambos os servidores e planeia apenas fazer uma alteração de configuração, certifique-se de que o seu servidor ativo e o servidor de encenação estão ambos a utilizar a mesma versão. Isso torna mais fácil comparar diferenças mais tarde. Se estiver a atualizar a partir do Azure AD Sync, estes servidores têm versões diferentes. Se estiver a atualizar-se a partir de uma versão mais antiga do Azure AD Connect, é uma boa ideia começar pelos dois servidores que estão a usar a mesma versão, mas não é necessário.
2. Se fez uma configuração personalizada e o seu servidor de encenação não a tem, siga os passos sob [mover uma configuração personalizada do servidor ativo para o servidor de encenação](#move-a-custom-configuration-from-the-active-server-to-the-staging-server).
3. Se estiver a atualizar a partir de um lançamento anterior do Azure AD Connect, atualize o servidor de encenação para a versão mais recente. Se estiver a mover-se do Azure AD Sync, instale o Azure AD Connect no seu servidor de encenação.
4. Deixe o motor sincronizado funcionar a importação completa e a sincronização completa no seu servidor de encenação.
5. Verifique se a nova configuração não provocou alterações inesperadas utilizando os passos em "Verificar" em [Verificar a configuração de um servidor](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server). Se algo não for como esperado, corrija-o, faça a importação e sincronize, e verifique os dados até que pareça bom, seguindo os passos.
6. Mude o servidor de encenação para ser o servidor ativo. Este é o passo final "Switch ative server" em [Verificar a configuração de um servidor](how-to-connect-sync-staging-server.md#verify-the-configuration-of-a-server).
7. Se estiver a atualizar o Azure AD Connect, atualize o servidor que está agora em modo de preparação para o mais recente lançamento. Siga os mesmos passos que antes para atualizar os dados e a configuração. Se atualizou a partir do Azure AD Sync, pode agora desligar e desativar o seu antigo servidor.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Mova uma configuração personalizada do servidor ativo para o servidor de encenação
Se elegisse alterações de configuração no servidor ativo, tem de se certificar de que as mesmas alterações são aplicadas ao servidor de encenação. Para ajudar neste movimento, pode utilizar o documentor de [configuração Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Pode mover as regras de sincronização personalizadas que criou utilizando o PowerShell. Tem de aplicar outras alterações da mesma forma em ambos os sistemas, e não pode migrar as alterações. O documentarista de [configuração](https://github.com/Microsoft/AADConnectConfigDocumenter) pode ajudá-lo a comparar os dois sistemas para se certificar de que são idênticos. A ferramenta também pode ajudar a automatizar os passos encontrados nesta secção.

É necessário configurar as seguintes coisas da mesma forma em ambos os servidores:

* Ligação às mesmas florestas
* Qualquer domínio e filtragem de OU
* As mesmas funcionalidades opcionais, tais como sincronização de palavras-passe e redação de palavras-passe

**Mover regras de sincronização personalizada**  
Para mover regras de sincronização personalizadas, faça o seguinte:

1. Open **Synchronization Rules Editor** no seu servidor ativo.
2. Selecione uma regra personalizada. Clique em **Exportar**. Isto traz uma janela de bloco de notas. Guarde o ficheiro temporário com uma extensão PS1. Isto faz dele um guião powerShell. Copie o ficheiro PS1 para o servidor de encenação.  
   ![Exportação de regras de sincronização](./media/how-to-upgrade-previous-version/exportrule.png)
3. O Connector GUID é diferente no servidor de encenação, e deve alterá-lo. Para obter o GUID, inicie o Editor de Regras de **Sincronização,** selecione uma das regras fora da caixa que representam o mesmo sistema conectado, e clique em **Exportar**. Substitua o GUID no seu ficheiro PS1 pelo GUID do servidor de encenação.
4. Num pedido powerShell, execute o ficheiro PS1. Isto cria a regra de sincronização personalizada no servidor de encenação.
5. Repita isto para todas as suas regras personalizadas.

## <a name="how-to-defer-full-synchronization-after-upgrade"></a>Como adiar a sincronização completa após a atualização
Durante a atualização no local, pode haver alterações que exijam atividades específicas de sincronização (incluindo o passo de importação completa e o passo de sincronização completa) a serem executadas. Por exemplo, as alterações do esquema do conector requerem alterações **completas** das regras de sincronização dos passos de importação e da regra de sincronização fora da caixa requerem que o passo completo de **sincronização** seja executado nos conectores afetados. Durante a atualização, o Azure AD Connect determina quais as atividades de sincronização necessárias e regista-as como *sobreposições*. No ciclo de sincronização seguinte, o programador de sincronização capta estas sobreposições e executa-as. Uma vez executada uma sobreposição com sucesso, é removida.

Pode haver situações em que não quer que estas substituições ocorram imediatamente após a atualização. Por exemplo, você tem inúmeros objetos sincronizados e você gostaria que estes passos de sincronização ocorram após o horário comercial. Para remover estas substituições:

1. Durante a atualização, **desfaça** a opção Iniciar o processo de **sincronização quando a configuração estiver concluída**. Isto desativa o programador de sincronização e impede que o ciclo de sincronização ocorra automaticamente antes de as substituições serem removidas.

   ![Desativação de sincronização](./media/how-to-upgrade-previous-version/disablefullsync01.png)

2. Após a atualização concluída, execute o seguinte cmdlet para saber quais as substituições adicionadas:`Get-ADSyncSchedulerConnectorOverride | fl`

   >[!NOTE]
   > As sobreposições são específicas do conector. No exemplo seguinte, o passo completo da importação e o passo de sincronização completa foram adicionados tanto ao Conector AD no local como ao Conector Azure AD.

   ![Desativação de sincronização](./media/how-to-upgrade-previous-version/disablefullsync02.png)

3. Note as sobreposições existentes que foram adicionadas.
   
4. Para remover as sobreposições para importação completa e sincronização completa num conector arbitrário, executar o seguinte cmdlet:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid-of-ConnectorIdentifier> -FullImportRequired $false -FullSyncRequired $false`

   Para remover as sobreposições em todos os conectores, execute o seguinte script PowerShell:

   ```
   foreach ($connectorOverride in Get-ADSyncSchedulerConnectorOverride)
   {
       Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier $connectorOverride.ConnectorIdentifier.Guid -FullSyncRequired $false -FullImportRequired $false
   }
   ```

5. Para retomar o programador, executar o seguinte cmdlet:`Set-ADSyncScheduler -SyncCycleEnabled $true`

   >[!IMPORTANT]
   > Lembre-se de executar os passos de sincronização necessários o mais rápido possível. Pode executar manualmente estes passos utilizando o Gestor de Serviço de Sincronização ou adicionar as substituições utilizando o cmdlet Set-ADSyncSchedulerConnectorOverride.

Para adicionar as sobreposições tanto para a importação completa como para a sincronização completa num conector arbitrário, executar o seguinte cmdlet:`Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> -FullImportRequired $true -FullSyncRequired $true`

## <a name="troubleshooting"></a>Resolução de problemas
A secção seguinte contém resolução de problemas e informações que pode utilizar se encontrar um problema de atualização do Azure AD Connect.

### <a name="azure-active-directory-connector-missing-error-during-azure-ad-connect-upgrade"></a>Erro perdido do conector ativo azure durante a atualização do Azure AD Connect

Ao atualizar o Azure AD Connect de uma versão anterior, poderá atingir o seguinte erro no início da atualização 

![Erro](./media/how-to-upgrade-previous-version/error1.png)

Este erro ocorre porque o conector Azure Ative Diretório com identificador, b891884f-051e-4a83-95af-2544101c9083, não existe na configuração atual do Azure AD Connect. Para verificar se este é o caso, abra uma janela PowerShell, executar Cmdlet`Get-ADSyncConnector -Identifier b891884f-051e-4a83-95af-2544101c9083`

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

O PowerShell Cmdlet relata o erro que **o MA especificado não pôde encontrar.**

A razão pela qual isto ocorre é porque a configuração atual do Azure AD Connect não é suportada para atualização. 

Se pretender instalar uma versão mais recente do Azure AD Connect: feche o assistente Azure AD Connect, desinstale o Azure AD Connect existente e efetue uma instalação limpa do mais recente Azure AD Connect.



## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a integração das suas identidades no local com o Diretório Ativo Azure.](whatis-hybrid-identity.md)
