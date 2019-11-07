---
title: 'Solucionar problemas de gerenciamento de Integration Runtime do SSIS no Azure Data Factory '
description: Este artigo fornece orientação para a solução de problemas de gerenciamento do SSIS Integration Runtime (SSIS IR)
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 3452fc2274eb646acb19c0e6a203ebadcb81cad5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684018"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Solucionar problemas de gerenciamento de Integration Runtime do SSIS no Azure Data Factory

Este artigo fornece orientação para a solução de problemas de gerenciamento no SSIS (Azure-SQL Server Integration Services) Integration Runtime (IR), também conhecido como IR do SSIS.

## <a name="overview"></a>Descrição geral

Se você tiver algum problema ao provisionar ou desprovisionar o SSIS IR, verá uma mensagem de erro no portal do Microsoft Azure Data Factory ou um erro retornado de um cmdlet do PowerShell. O erro sempre aparece no formato de um código de erro com uma mensagem de erro detalhada.

Se o código de erro for InternalServerError, o serviço terá problemas transitórios e você deverá repetir a operação mais tarde. Se uma nova tentativa não ajudar, entre em contato com a equipe de suporte do Azure Data Factory.

Caso contrário, três principais dependências externas podem causar erros: um servidor de banco de dados SQL do Azure ou uma instância gerenciada, um script de instalação personalizada e uma configuração de rede virtual.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problemas de servidor do banco de dados SQL do Azure ou instância gerenciada

Será necessário um servidor da Base de Dados SQL do Azure ou uma instância gerida se estiver a aprovisionar o SSIS IR com uma base de dados de catálogo do SSIS. O SSIS IR deve ser capaz de aceder ao servidor da Base de Dados SQL do Azure ou à instância gerida. Além disso, a conta do servidor da Base de Dados SQL do Azure ou da instância gerida deve ter permissão para criar um base de dados de catálogo do SSIS (SSISDB). Se ocorrer algum erro, será apresentado um código de erro com uma mensagem de exceção SQL detalhada no portal do Data Factory. Utilize as informações na lista a seguir para resolver problemas de códigos de erro.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Poderá ver este problema quando estiver a aprovisionar um novo SSIS IR ou enquanto o runtime de integração estiver em execução. Se ocorrer este erro durante o aprovisionamento do runtime de integração, poderá obter uma mensagem de SqlException detalhada na mensagem de erro que indica um dos seguintes problemas:

* Um problema de ligação de rede. Verifique se o SQL Server ou nome do anfitrião da instância gerida está acessível. Verifique também se nenhuma firewall ou grupo de segurança de rede (NSG) está a bloquear o acesso do SSIS IR ao servidor.
* Falha no início de sessão durante a autenticação do SQL. A conta fornecida não consegue iniciar sessão na base de dados do SQL Server. Confirme que indica a conta de utilizador correta.
* Falha de início de sessão durante a autenticação do Microsoft Azure Active Directory (Microsoft Azure AD) (identidade gerida). Adicione a identidade gerida da fábrica a um grupo do AAD e verifique se a identidade gerida tem permissões de acesso ao servidor da base de dados de catálogo.
* Limite de tempo da ligação excedido. Este erro é sempre causado por uma configuração relacionada com a segurança. É recomendável que:
  1. Crie uma nova VM.
  1. Ingresse a VM no mesmo Rede Virtual do Microsoft Azure de IR se o IR estiver em uma rede virtual.
  1. Instale o SSMS e verifique o servidor do banco de dados SQL do Azure ou o status da instância gerenciada.

Para outros problemas, corrija o problema mostrado na mensagem de erro de exceção SQL detalhada. Se os problemas persistirem, contacte a equipa de suporte do servidor da Base de Dados SQL do Azure ou da instância gerida.

Se vir o erro quando o runtime de integração estiver em execução, as alterações da firewall ou do grupo de segurança de rede poderão estar a impedir o acesso do nó de trabalho do SSIS IR ao servidor da Base de Dados SQL ou à instância gerida. Desbloqueie o nó de trabalho do SSIS IR para que possa aceder ao servidor da Base de Dados SQL do Azure ou à instância gerida.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Este tipo de mensagem de erro pode parecer com o seguinte: "o banco de dados ' SSISDB ' atingiu sua cota de tamanho. Particione ou exclua dados, remova índices ou consulte a documentação para obter as possíveis resoluções. " 

Soluções possíveis:
* Aumente o tamanho da cota do SSISDB.
* Alterar a configuração do SSISDB para reduzir o tamanho, ao:
   * Reduzindo o período de retenção e o número de versões do projeto.
   * Reduzindo o período de retenção do log.
   * Alterando o nível padrão do log.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Este erro significa que o servidor da Base de Dados SQL do Azure ou a instância gerida já tem um SSISDB e que está a ser utilizado por outro runtime de integração. Precisa de indicar um servidor da Base de Dados SQL do Azure ou uma instância gerida diferente ou eliminar o SSISDB existente e reiniciar o novo runtime de integração.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Este erro pode ocorrer por um dos seguintes motivos:

* A conta de utilizador que está configurada para o SSIS IR não tem permissão para criar a base de dados. Pode conceder ao utilizador a permissão para criar a base de dados.
* Ocorre um erro de tempo limite durante a criação da base de dados, como um tempo limite de execução ou um tempo limite de operação da BD. Deve repetir a operação mais tarde. Se a operação de repetição não der resultado, contacte a equipa de suporte do servidor da Base de Dados SQL do Azure ou da Instância Gerida.

Para outros problemas, verifique a Mensagem de erro de exceção do SQL e corrija o problema mencionado nos detalhes do erro. Se os problemas persistirem, contacte a equipa de suporte do servidor da Base de Dados SQL do Azure ou da instância gerida.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Esse tipo de mensagem de erro é semelhante a: "nome do objeto inválido ' Catalog. catalog_properties '." Nessa situação, você já tem um banco de dados chamado SSISDB, mas ele não foi criado pelo IR do SSIS ou o banco de dados está em um estado inválido causado por erros no último provisionamento de IR do SSIS. Pode remover a base de dados existente com o nome SSISDB ou pode configurar um novo servidor da Base de Dados SQL do Azure ou uma instância gerida para o runtime de integração.

## <a name="custom-setup-issues"></a>Problemas de instalação personalizada

A configuração personalizada fornece uma interface para adicionar os seus próprios passos de configuração durante o aprovisionamento ou a reconfiguração do SSIS IR. Para obter mais informações, veja [Personalizar a configuração do Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Verifique se o contentor contém apenas os ficheiros de configuração personalizados necessários; todos os ficheiros no contentor serão transferidos para o nó de trabalho do SSIS IR. Recomendamos que teste o script de configuração personalizada num computador local para corrigir qualquer problema de execução do script antes de o executar no SSIS IR.

O contentor do script de configuração personalizada será verificado enquanto o IR estiver em execução, pois o SSIS IR é atualizado regularmente. Esta atualização requer o acesso ao contentor para transferir o script de configuração personalizada e instalá-lo novamente. O processo também verifica se o contentor está acessível e se existe o ficheiro main.cmd.

Para qualquer erro que envolva a instalação personalizada, você verá um código de erro CustomSetupScriptFailure com o subcódigo, como CustomSetupScriptBlobContainerInaccessible ou CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Este erro significa que o SSIS IR não conseguiu aceder ao contentor de blobs do Azure para a configuração personalizada. Verifique se o URI de SAS do contentor está acessível e se não expirou.

Pare o IR se estiver em execução, reconfigure o IR com o novo URI de SAS do contentor de configuração personalizada e reinicie-o.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Este erro significa que o SSIS IR não consegue encontrar um script de configuração personalizada (main.cmd) no contentor de blobs. Verifique se o ficheiro main.cmd existe no contentor, que é o ponto de entrada para a configuração personalizada.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Este erro significa que a execução do script de configuração personalizada (main.cmd) falhou. Experimente o script no computador local primeiro ou verifique os registos de execução da configuração personalizada no contentor de blobs.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Este erro indica que foi excedido o tempo limite de execução do script de configuração personalizada. Verifique se o script pode ser executado silenciosamente e nenhuma entrada interativa necessária e se o contêiner de BLOB contém apenas os arquivos de instalação personalizados necessários. É recomendável testar o script no computador local primeiro. Também deve verificar os registos de execução da configuração personalizada no contentor de blobs. O período máximo da configuração personalizada é de 45 minutos antes de exceder o limite de tempo. O período máximo inclui o tempo para transferir todos os ficheiros do contentor e instalá-los no SSIS IR. Se precisar de um período mais extenso, crie um pedido de suporte.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Este erro significa que a tentativa de carregamento dos registos de execução da configuração personalizada para o contentor de blobs falhou. O problema ocorre porque o SSIS IR não tem permissões de escrita no contentor de blobs ou devido a problemas de armazenamento ou rede. Se a configuração personalizada for bem sucedida, este erro não afetará nenhuma função do SSIS, mas os registos estarão ausentes. Se a configuração personalizada falhar com outro erro e o registo não for carregado, este erro será comunicado em primeiro para que o registo possa ser carregado para análise. Além disso, depois deste problema ter sido resolvido, comunicaremos eventuais problemas mais específicos. Se este problema não ficar resolvido após uma nova tentativa, contacte a equipa de suporte do Azure Data Factory.

## <a name="virtual-network-configuration"></a>Configuração de rede virtual

Quando associa o SSIS IR à Rede Virtual do Microsoft Azure, o SSIS IR utiliza a rede virtual que está na subscrição do utilizador. Para obter mais informações, veja [Associar um Azure-SSIS Integration Runtime a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Quando ocorrer um problema relacionado com a Rede Virtual, verá um dos erros a seguir.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Este erro pode ocorrer por vários motivos. Para resolver este erro, veja as secções [Proibido](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) e [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Proibido

Esse tipo de erro pode ser semelhante a este: "Subnetid não está habilitado para a conta atual. O provedor de recursos Microsoft. batch não está registrado na mesma assinatura da VNet. "

Estes detalhes significam que o Azure Batch não consegue aceder à rede virtual. Registe o fornecedor de recursos do Microsoft.Batch na mesma subscrição da Rede Virtual.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Este tipo de erro pode ser semelhante a um dos seguintes: 

- "A VNet especificada não existe ou o serviço de lote não tem acesso a ela."
- "A sub-rede xxx especificada não existe."

Estes erros significam que a rede virtual não existe, o serviço do Azure Batch não pode aceder à rede ou a sub-rede fornecida não existe. Verifique se a rede virtual e a sub-rede existem e se o Azure Batch tem acesso.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Esse tipo de mensagem de erro pode ter a seguinte aparência: "falha ao provisionar Integration Runtime na VNet. Se as configurações do servidor DNS ou do NSG estiverem configuradas, verifique se o servidor DNS está acessível e se NSG está configurado corretamente. "

Nesta situação, provavelmente tem uma configuração personalizada do servidor DNS ou definições do NSG, o que impede que o nome do servidor do Azure requerido pelo SSIS IR seja resolvido ou acedido. Para obter mais informações, veja [Configuração da Rede Virtual do SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se os problemas persistirem, contacte a equipa de suporte do Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

O SSIS IR será atualizado automaticamente de forma regular. Um novo conjunto do Azure Batch é criado durante a atualização e o conjunto do Azure Batch antigo é eliminado. Além disso, os recursos relacionados com a Rede Virtual do conjunto antigo são eliminados e os novos recursos relacionados com a Rede Virtual são criados na sua subscrição. Este erro significa que a eliminação de recursos relacionados com a Rede Virtual do conjunto antigo falhou devido a um bloqueio de eliminação ao nível da subscrição ou do grupo de recursos. Como o cliente controla e define o bloqueio de eliminação, o bloqueio de eliminação deve ser removido nesta situação.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Se o aprovisionamento do SSIS IR falhar, todos os recursos criados serão eliminados. No entanto, se existir um bloqueio de eliminação de recursos ao nível da subscrição ou do grupo de recursos, os recursos da Rede Virtual não serão eliminados conforme o esperado. Para corrigir este erro, remova o bloqueio de eliminação e reinicie o IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Quando parar o SSIS IR, todos os recursos relacionados com a Rede Virtual serão eliminados. Mas a eliminação poderá falhar se existir um bloqueio de eliminação de recursos ao nível da subscrição ou do grupo de recursos. Também aqui o cliente controla e define o bloqueio de eliminação. Portanto, o cliente deve remover o bloqueio de eliminação e parar o SSIS IR novamente.

### <a name="nodeunavailable"></a>NodeUnavailable

Este erro ocorre quando o IR está em execução e significa que o IR passou a estar em mau estado de funcionamento. Este erro é sempre causado por uma alteração no servidor DNS ou na configuração NSG que impede que o SSIS IR se ligue a um serviço necessário. Como a configuração do servidor DNS e do NSG é controlada pelo cliente, o cliente deve corrigir os problemas de bloqueio no seu lado. Para obter mais informações, veja [Configuração da Rede Virtual do SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se os problemas persistirem, contacte a equipa de suporte do Azure Data Factory.
