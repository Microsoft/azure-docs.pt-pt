---
title: Resolução de problemas SSIS Integração Gestão de tempo de execução
description: Este artigo fornece orientações de resolução de problemas para questões de gestão do Tempo de Execução da Integração SSIS (SSIS IR)
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/08/2019
ms.openlocfilehash: 5f1332255ae83a32f9b71d24d812b00fad9b7fa1
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637926"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Resolução de problemas SSIS Integração Gestão de tempo de execução na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo fornece orientações de resolução de problemas para problemas de gestão em Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR), também conhecido como SSIS IR.

## <a name="overview"></a>Descrição geral

Se encontrar algum problema durante o provisionamento ou desprovisionamento do SSIS IR, verá uma mensagem de erro no portal Microsoft Azure Data Factory ou um erro devolvido de um cmdlet PowerShell. O erro aparece sempre no formato de um código de erro com uma mensagem de erro detalhada.

Se o código de erro for InternalServerError, o serviço tem problemas transitórios e deverá voltar a tentar a operação mais tarde. Se uma nova mente não ajudar, contacte a equipa de apoio da Azure Data Factory.

Caso contrário, três grandes dependências externas podem causar erros: Azure SQL Database ou Azure SQL Managed Instance, um script de configuração personalizado e uma configuração de rede virtual.

## <a name="sql-database-or-sql-managed-instance-issues"></a>Sql Database ou SQL Gerenciados Problemas de Caso

A Base de Dados SQL ou a SQL Managed Instance são necessárias se estiver a aacionar o SSIS IR com uma base de dados de catálogo SSIS. O SSIS IR deve poder aceder à Base de Dados SQL ou à SQL Managed Instance. Além disso, a conta de login da SQL Database ou sql Managed Instance deve ter permissão para criar uma base de dados de catálogo SSIS (SSISDB). Se ocorrer algum erro, será apresentado um código de erro com uma mensagem de exceção SQL detalhada no portal do Data Factory. Utilize as informações na lista a seguir para resolver problemas de códigos de erro.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Poderá ver este problema quando estiver a aprovisionar um novo SSIS IR ou enquanto o runtime de integração estiver em execução. Se ocorrer este erro durante o aprovisionamento do runtime de integração, poderá obter uma mensagem de SqlException detalhada na mensagem de erro que indica um dos seguintes problemas:

* Um problema de ligação de rede. Verifique se o nome de anfitrião da SQL Database ou sql Managed Instance está acessível. Verifique também se nenhuma firewall ou grupo de segurança de rede (NSG) está a bloquear o acesso do SSIS IR ao servidor.
* Falha no início de sessão durante a autenticação do SQL. A conta fornecida não consegue iniciar sessão na base de dados do SQL Server. Confirme que indica a conta de utilizador correta.
* Falha de início de sessão durante a autenticação do Microsoft Azure Active Directory (Microsoft Azure AD) (identidade gerida). Adicione a identidade gerida da fábrica a um grupo do AAD e verifique se a identidade gerida tem permissões de acesso ao servidor da base de dados de catálogo.
* Limite de tempo da ligação excedido. Este erro é sempre causado por uma configuração relacionada com a segurança. É recomendável que:
  1. Criar um novo VM.
  1. Junte-se ao VM à mesma Rede Virtual Microsoft Azure de IR se o IR estiver numa rede virtual.
  1. Instale SSMS e verifique o estado da Base de Dados SQL ou do sql Managed Instance.

Para outros problemas, corrija o problema mostrado na mensagem de erro de exceção SQL detalhada. Se ainda tiver problemas, contacte a Base de Dados SQL ou a equipa de apoio à SQL Managed Instance.

Se vir o erro quando o IR está em execução, o grupo de segurança da rede ou as alterações de firewall estão provavelmente a impedir que o nó do trabalhador do SSIS IR aceda à Base de Dados SQL ou à SqL Managed Instance. Desbloqueie o nó do trabalhador SSIS IR para que possa aceder à Base de Dados SQL ou à SQL Managed Instance.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Eis o que este tipo de mensagem de erro pode parecer: "A base de dados 'SSISDB' atingiu a sua quota de tamanho. Dividir ou apagar dados, deixar cair índices ou consultar a documentação para possíveis resoluções." 

Soluções possíveis:
* Aumente o tamanho da quota do seu SSISDB.
* Alterar a configuração do SSISDB para reduzir o tamanho, ao:
   * Redução do período de retenção e do número de versões do projeto.
   * Reduzindo o período de retenção do tronco.
   * Alterar o nível predefinido do registo.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Este erro significa que a SQL Database ou a SQL Managed Instance já tem um SSISDB e que está a ser usada por outro IR. Tem de fornecer uma base de dados sql diferente ou uma instância gerida do SQL ou eliminar o SSISDB existente e reiniciar o novo IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Este erro pode ocorrer por um dos seguintes motivos:

* A conta de utilizador que está configurada para o SSIS IR não tem permissão para criar a base de dados. Pode conceder ao utilizador a permissão para criar a base de dados.
* Ocorre um erro de tempo limite durante a criação da base de dados, como um tempo limite de execução ou um tempo limite de operação da BD. Deve repetir a operação mais tarde. Se a redação não funcionar, contacte a Base de Dados SQL ou a equipa de apoio sql Managed Instance.

Para outros problemas, verifique a Mensagem de erro de exceção do SQL e corrija o problema mencionado nos detalhes do erro. Se ainda tiver problemas, contacte a Base de Dados SQL ou a equipa de apoio à SQL Managed Instance.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Este tipo de mensagem de erro parece-se com isto: "Nome de objeto inválido 'catalog.catalog_properties'." Nesta situação, ou já tem uma base de dados chamada SSISDB, mas não foi criada pelo SSIS IR, ou a base de dados está num estado inválido que é causado por erros na última provisão do IR SSIS. Pode deixar cair a base de dados existente com o nome SSISDB, ou pode configurar uma nova Base de Dados SQL ou sql Gestd Instance para o IR.

## <a name="custom-setup-issues"></a>Problemas de configuração personalizados

A configuração personalizada fornece uma interface para adicionar os seus próprios passos de configuração durante o aprovisionamento ou a reconfiguração do SSIS IR. Para obter mais informações, veja [Personalizar a configuração do Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md).

Verifique se o contentor contém apenas os ficheiros de configuração personalizados necessários; todos os ficheiros no contentor serão transferidos para o nó de trabalho do SSIS IR. Recomendamos que teste o script de configuração personalizada num computador local para corrigir qualquer problema de execução do script antes de o executar no SSIS IR.

O contentor do script de configuração personalizada será verificado enquanto o IR estiver em execução, pois o SSIS IR é atualizado regularmente. Esta atualização requer o acesso ao contentor para transferir o script de configuração personalizada e instalá-lo novamente. O processo também verifica se o contentor está acessível e se existe o ficheiro main.cmd.

Para qualquer erro que envolva configuração personalizada, verá um código de erro CustomSetupScriptFailure com código de sub código como CustomSetupScriptBlobContainerInaccessible ou CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Este erro significa que o SSIS IR não conseguiu aceder ao contentor de blobs do Azure para a configuração personalizada. Verifique se o URI de SAS do contentor está acessível e se não expirou.

Pare o IR se estiver em execução, reconfigure o IR com o novo URI de SAS do contentor de configuração personalizada e reinicie-o.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Este erro significa que o SSIS IR não consegue encontrar um script de configuração personalizada (main.cmd) no contentor de blobs. Verifique se o ficheiro main.cmd existe no contentor, que é o ponto de entrada para a configuração personalizada.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Este erro significa que a execução do script de configuração personalizada (main.cmd) falhou. Experimente o script no computador local primeiro ou verifique os registos de execução da configuração personalizada no contentor de blobs.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Este erro indica que foi excedido o tempo limite de execução do script de configuração personalizada. Garanta que o script pode ser executado silenciosamente, e que não são necessárias entradas interativa, e verifique se o contentor de blobs contem apenas os ficheiros de configuração personalizada necessários. Recomenda-se testar o script no computador local primeiro. Também deve verificar os registos de execução da configuração personalizada no contentor de blobs. O período máximo da configuração personalizada é de 45 minutos antes de exceder o limite de tempo. O período máximo inclui o tempo para transferir todos os ficheiros do contentor e instalá-los no SSIS IR. Se precisar de um período mais extenso, crie um pedido de suporte.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Este erro significa que a tentativa de carregamento dos registos de execução da configuração personalizada para o contentor de blobs falhou. O problema ocorre porque o SSIS IR não tem permissões de escrita no contentor de blobs ou devido a problemas de armazenamento ou rede. Se a configuração personalizada for bem sucedida, este erro não afetará nenhuma função do SSIS, mas os registos estarão ausentes. Se a configuração personalizada falhar com outro erro e o registo não for carregado, este erro será comunicado em primeiro para que o registo possa ser carregado para análise. Além disso, depois deste problema ter sido resolvido, comunicaremos eventuais problemas mais específicos. Se este problema não ficar resolvido após uma nova tentativa, contacte a equipa de suporte do Azure Data Factory.

## <a name="virtual-network-configuration"></a>Configuração de rede virtual

Quando associa o SSIS IR à Rede Virtual do Microsoft Azure, o SSIS IR utiliza a rede virtual que está na subscrição do utilizador. Para obter mais informações, veja [Associar um Azure-SSIS Integration Runtime a uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md).
Depois de o SSIS IR começar com sucesso, se encontrar problemas de ligação à rede, pode tentar utilizar a [ferramenta de conectividade de diagnóstico](ssis-integration-runtime-diagnose-connectivity-faq.md) para diagnosticar o problema por si mesmo.
Quando ocorrer um problema relacionado com a Rede Virtual, verá um dos erros a seguir.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Este erro pode ocorrer por vários motivos. Para resolver este erro, veja as secções [Proibido](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) e [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Proibido

Este tipo de erro pode assemelhar-se a isto: "O SubnetId não está ativado para a conta corrente. Microsoft.Batfornecedor de recursos ch não está registado na mesma subscrição da VNet."

Estes detalhes significam que o Azure Batch não consegue aceder à rede virtual. Registe o fornecedor de recursos do Microsoft.Batch na mesma subscrição da Rede Virtual.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Este tipo de erro pode ser semelhante a um dos seguintes: 

- "Ou o VNet especificado não existe, ou o serviço Batch não tem acesso a ele."
- "O sub-rede especificado xxx não existe."

Estes erros significam que a rede virtual não existe, o serviço do Azure Batch não pode aceder à rede ou a sub-rede fornecida não existe. Verifique se a rede virtual e a sub-rede existem e se o Azure Batch tem acesso.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Este tipo de mensagem de erro pode ser assim: "Falhou na disposição do tempo de execução da integração em VNet. Se as definições do servidor DNS ou NSG estiverem configuradas, certifique-se de que o servidor DNS está acessível e que o NSG está configurado corretamente."

Nesta situação, provavelmente tem uma configuração personalizada do servidor DNS ou definições do NSG, o que impede que o nome do servidor do Azure requerido pelo SSIS IR seja resolvido ou acedido. Para obter mais informações, veja [Configuração da Rede Virtual do SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md). Se os problemas persistirem, contacte a equipa de suporte do Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

O SSIS IR será atualizado automaticamente de forma regular. Um novo conjunto do Azure Batch é criado durante a atualização e o conjunto do Azure Batch antigo é eliminado. Além disso, os recursos relacionados com a Rede Virtual do conjunto antigo são eliminados e os novos recursos relacionados com a Rede Virtual são criados na sua subscrição. Este erro significa que a eliminação de recursos relacionados com a Rede Virtual do conjunto antigo falhou devido a um bloqueio de eliminação ao nível da subscrição ou do grupo de recursos. Como o cliente controla e define o bloqueio de eliminação, o bloqueio de eliminação deve ser removido nesta situação.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Se o aprovisionamento do SSIS IR falhar, todos os recursos criados serão eliminados. No entanto, se existir um bloqueio de eliminação de recursos ao nível da subscrição ou do grupo de recursos, os recursos da Rede Virtual não serão eliminados conforme o esperado. Para corrigir este erro, remova o bloqueio de eliminação e reinicie o IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Quando parar o SSIS IR, todos os recursos relacionados com a Rede Virtual serão eliminados. Mas a eliminação poderá falhar se existir um bloqueio de eliminação de recursos ao nível da subscrição ou do grupo de recursos. Também aqui o cliente controla e define o bloqueio de eliminação. Portanto, o cliente deve remover o bloqueio de eliminação e parar o SSIS IR novamente.

### <a name="nodeunavailable"></a>NodeUnavailable

Este erro ocorre quando o IR está em execução e significa que o IR passou a estar em mau estado de funcionamento. Este erro é sempre causado por uma alteração no servidor DNS ou na configuração NSG que impede que o SSIS IR se ligue a um serviço necessário. Como a configuração do servidor DNS e do NSG é controlada pelo cliente, o cliente deve corrigir os problemas de bloqueio no seu lado. Para obter mais informações, veja [Configuração da Rede Virtual do SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md). Se os problemas persistirem, contacte a equipa de suporte do Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Configuração de endereços IP públicos estáticos

Ao aderir à Rede Virtual Azure-SSIS para Azure, também é capaz de trazer os seus próprios endereços IP públicos estáticos para o IR para que o IR possa aceder a fontes de dados que limitam o acesso a endereços IP específicos. Para obter mais informações, veja [Associar um Azure-SSIS Integration Runtime a uma rede virtual](./join-azure-ssis-integration-runtime-virtual-network.md).

Além dos problemas de rede virtual acima, pode também encontrar-se com questões estáticas relacionadas com endereços IP públicos. Verifique os seguintes erros para obter ajuda.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InvalidPublicipSpecified

Este erro pode ocorrer por uma variedade de razões quando iniciar o Azure-SSIS IR:

| Mensagem de erro | Solução|
|:--- |:--- |
| O endereço IP público estático fornecido já está a ser utilizado, por favor forneça dois não utilizados para o seu Tempo de Execução de Integração Azure-SSIS. | Deve selecionar dois endereços IP públicos estáticos não uusados ou remover as referências atuais ao endereço IP público especificado e, em seguida, reiniciar o Azure-SSIS IR. |
| O endereço IP público estático fornecido não tem nome DNS, por favor forneça dois deles com o nome DNS para o seu Tempo de Execução de Integração Azure-SSIS. | Pode configurar o nome DNS do endereço IP público no portal Azure, como mostra a imagem abaixo. Passos específicos são os seguintes: (1) Abrir portal Azure e goto a página de recursos deste endereço IP público; (2) Selecione a secção **de Configuração** e configurar o nome DNS e, em seguida, clique em **Guardar** o botão; (3) Reinicie o seu Azure-SSIS IR. |
| Os endereços IP públicos VNet fornecidos e estáticos para o seu tempo de funcionamento de integração Azure-SSIS devem estar no mesmo local. | De acordo com os requisitos da Rede Azure, o endereço IP público estático e a rede virtual devem estar no mesmo local e subscrição. Por favor, forneça dois endereços IP públicos estáticos válidos e reinicie o Azure-SSIS IR. |
| O endereço IP público estático fornecido é básico, por favor forneça dois padrão para o seu Tempo de Execução de Integração Azure-SSIS. | Consulte [skus do Endereço IP Público](../virtual-network/public-ip-addresses.md#sku) para obter ajuda. |

![Runtime de integração Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Se o provisionamento do IR Azure-SSIS falhar, todos os recursos que foram criados são eliminados. No entanto, se houver um bloqueio de eliminação de recursos no nível de subscrição ou grupo de recursos (que contém o seu endereço IP público estático), os recursos da rede não são eliminados como esperado. Para corrigir o erro, retire o bloqueio de eliminação e reinicie o IR.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedduringstop

Quando parar o Azure-SSIS IR, todos os recursos de rede criados no grupo de recursos que contenham o seu endereço IP público serão eliminados. Mas a eliminação pode falhar se houver um bloqueio de exclusão de recursos no nível de subscrição ou grupo de recursos (que contém o seu endereço IP público estático). Por favor, retire o bloqueio de apagar e reinicie o IR.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

O Azure-SSIS IR é atualizado automaticamente regularmente. Novos nós de IR são criados durante a atualização e os nós antigos serão eliminados. Além disso, os recursos de rede criados (por exemplo, o balanceador de carga e o grupo de segurança da rede) para os nós antigos são eliminados, e os novos recursos de rede são criados sob a sua subscrição. Este erro significa que a eliminação dos recursos de rede para os nós antigos falhou devido a um bloqueio de eliminação no nível de subscrição ou grupo de recursos (que contém o seu endereço IP público estático). Por favor, retire o bloqueio de eliminação para que possamos limpar os nós antigos e libertar o endereço IP público estático para os nós antigos. Caso contrário, o endereço IP público estático não pode ser lançado e não poderemos atualizar ainda mais o seu IR.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsableDuringUpgrade

Quando pretender trazer os seus próprios endereços IP públicos estáticos, devem ser fornecidos dois endereços IP públicos. Um deles será usado para criar os nós de INFRAVERMELHOs imediatamente e outro será usado durante a atualização do IR. Este erro pode ocorrer quando o outro endereço IP público não é utilizável durante a atualização. Consulte o  [InvalidPublicIPSpecified](#InvalidPublicIPSpecified) para possíveis causas.