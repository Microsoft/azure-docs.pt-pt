---
title: Resolução de problemas SSIS Integração Gestão de tempo de execução
description: Este artigo fornece orientações de resolução de problemas para questões de gestão do Runtime de Integração ssis (SSIS IR)
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
ms.openlocfilehash: 0324044d93f12f6ac6ec96ff1a31be8ee02ada41
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414699"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Resolução de problemas SSIS Integração Gestão de tempo de execução na Fábrica de Dados Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo fornece orientações de resolução de problemas para problemas de gestão em Serviços de Integração de Servidores Azure-SQL (SSIS) Integration Runtime (IR), também conhecido como SSIS IR.

## <a name="overview"></a>Descrição geral

Se tiver algum problema durante o fornecimento ou desprovisionamento do SSIS IR, verá uma mensagem de erro no portal Microsoft Azure Data Factory ou um erro devolvido a partir de um cmdlet PowerShell. O erro aparece sempre no formato de um código de erro com uma mensagem de erro detalhada.

Se o código de erro for InternalServerError, o serviço tem problemas transitórios e deverá voltar a tentar a operação mais tarde. Se uma nova tentativa não ajudar, contacte a equipa de suporte da Azure Data Factory.

Caso contrário, três grandes dependências externas podem causar erros: um servidor de base de dados Azure SQL ou instância gerida, um script de configuração personalizado e uma configuração de rede virtual.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Servidor de base de dados Azure SQL ou problemas de instância geridos

Será necessário um servidor da Base de Dados SQL do Azure ou uma instância gerida se estiver a aprovisionar o SSIS IR com uma base de dados de catálogo do SSIS. O SSIS IR deve ser capaz de aceder ao servidor da Base de Dados SQL do Azure ou à instância gerida. Além disso, a conta do servidor da Base de Dados SQL do Azure ou da instância gerida deve ter permissão para criar um base de dados de catálogo do SSIS (SSISDB). Se ocorrer algum erro, será apresentado um código de erro com uma mensagem de exceção SQL detalhada no portal do Data Factory. Utilize as informações na lista a seguir para resolver problemas de códigos de erro.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Poderá ver este problema quando estiver a aprovisionar um novo SSIS IR ou enquanto o runtime de integração estiver em execução. Se ocorrer este erro durante o aprovisionamento do runtime de integração, poderá obter uma mensagem de SqlException detalhada na mensagem de erro que indica um dos seguintes problemas:

* Um problema de ligação de rede. Verifique se o SQL Server ou nome do anfitrião da instância gerida está acessível. Verifique também se nenhuma firewall ou grupo de segurança de rede (NSG) está a bloquear o acesso do SSIS IR ao servidor.
* Falha no início de sessão durante a autenticação do SQL. A conta fornecida não consegue iniciar sessão na base de dados do SQL Server. Confirme que indica a conta de utilizador correta.
* Falha de início de sessão durante a autenticação do Microsoft Azure Active Directory (Microsoft Azure AD) (identidade gerida). Adicione a identidade gerida da fábrica a um grupo do AAD e verifique se a identidade gerida tem permissões de acesso ao servidor da base de dados de catálogo.
* Limite de tempo da ligação excedido. Este erro é sempre causado por uma configuração relacionada com a segurança. É recomendável que:
  1. Crie um novo VM.
  1. Junte-se ao VM à mesma Rede Virtual de IR do Microsoft Azure se o IR estiver numa rede virtual.
  1. Instale SSMS e verifique o servidor de base de dados Azure SQL ou o estado de instância gerido.

Para outros problemas, corrija o problema mostrado na mensagem de erro de exceção SQL detalhada. Se os problemas persistirem, contacte a equipa de suporte do servidor da Base de Dados SQL do Azure ou da instância gerida.

Se vir o erro quando o runtime de integração estiver em execução, as alterações da firewall ou do grupo de segurança de rede poderão estar a impedir o acesso do nó de trabalho do SSIS IR ao servidor da Base de Dados SQL ou à instância gerida. Desbloqueie o nó de trabalho do SSIS IR para que possa aceder ao servidor da Base de Dados SQL do Azure ou à instância gerida.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Eis o que este tipo de mensagem de erro pode parecer: "A base de dados 'SSISDB' atingiu a sua quota de tamanho. Partição ou eliminação de dados, queda de índices ou consulte a documentação para eventuais resoluções." 

Soluções possíveis:
* Aumente o tamanho da quota do seu SSISDB.
* Alterar a configuração do SSISDB para reduzir o tamanho, ao:
   * Redução do período de retenção e número de versões de projeto.
   * Reduzindo o período de retenção do tronco.
   * Alterar o nível padrão do registo.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Este erro significa que o servidor da Base de Dados SQL do Azure ou a instância gerida já tem um SSISDB e que está a ser utilizado por outro runtime de integração. Precisa de indicar um servidor da Base de Dados SQL do Azure ou uma instância gerida diferente ou eliminar o SSISDB existente e reiniciar o novo runtime de integração.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Este erro pode ocorrer por um dos seguintes motivos:

* A conta de utilizador que está configurada para o SSIS IR não tem permissão para criar a base de dados. Pode conceder ao utilizador a permissão para criar a base de dados.
* Ocorre um erro de tempo limite durante a criação da base de dados, como um tempo limite de execução ou um tempo limite de operação da BD. Deve repetir a operação mais tarde. Se a operação de repetição não der resultado, contacte a equipa de suporte do servidor da Base de Dados SQL do Azure ou da Instância Gerida.

Para outros problemas, verifique a Mensagem de erro de exceção do SQL e corrija o problema mencionado nos detalhes do erro. Se os problemas persistirem, contacte a equipa de suporte do servidor da Base de Dados SQL do Azure ou da instância gerida.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Este tipo de mensagem de erro é assim: "Nome de objeto inválido 'catalog.catalog_properties'." Nesta situação, ou já tem uma base de dados chamada SSISDB mas não foi criada pelo SSIS IR, ou a base de dados está num estado inválido que é causado por erros no último fornecimento de IR SSIS. Pode remover a base de dados existente com o nome SSISDB ou pode configurar um novo servidor da Base de Dados SQL do Azure ou uma instância gerida para o runtime de integração.

## <a name="custom-setup-issues"></a>Problemas de configuração personalizados

A configuração personalizada fornece uma interface para adicionar os seus próprios passos de configuração durante o aprovisionamento ou a reconfiguração do SSIS IR. Para obter mais informações, veja [Personalizar a configuração do Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Verifique se o contentor contém apenas os ficheiros de configuração personalizados necessários; todos os ficheiros no contentor serão transferidos para o nó de trabalho do SSIS IR. Recomendamos que teste o script de configuração personalizada num computador local para corrigir qualquer problema de execução do script antes de o executar no SSIS IR.

O contentor do script de configuração personalizada será verificado enquanto o IR estiver em execução, pois o SSIS IR é atualizado regularmente. Esta atualização requer o acesso ao contentor para transferir o script de configuração personalizada e instalá-lo novamente. O processo também verifica se o contentor está acessível e se existe o ficheiro main.cmd.

Para qualquer erro que envolva configuração personalizada, verá um código de erro CustomSetupScriptFailure com um sub código como CustomSetupScriptBlobContainerInaccessible ou CustomSetupScriptNotFound.

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

## <a name="virtual-network-configuration"></a>Configuração da rede virtual

Quando associa o SSIS IR à Rede Virtual do Microsoft Azure, o SSIS IR utiliza a rede virtual que está na subscrição do utilizador. Para obter mais informações, veja [Associar um Azure-SSIS Integration Runtime a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Quando ocorrer um problema relacionado com a Rede Virtual, verá um dos erros a seguir.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Este erro pode ocorrer por vários motivos. Para resolver este erro, veja as secções [Proibido](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) e [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Proibido

Este tipo de erro pode assemelhar-se a este: "O SubnetId não está ativado para a conta corrente. Microsoft.Batch fornecedor de recursos não está registado sob a mesma subscrição da VNet."

Estes detalhes significam que o Azure Batch não consegue aceder à rede virtual. Registe o fornecedor de recursos do Microsoft.Batch na mesma subscrição da Rede Virtual.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Este tipo de erro pode ser semelhante a um dos seguintes: 

- "Ou a VNet especificada não existe, ou o serviço Batch não tem acesso a ele."
- "A subnet xxx especificada não existe."

Estes erros significam que a rede virtual não existe, o serviço do Azure Batch não pode aceder à rede ou a sub-rede fornecida não existe. Verifique se a rede virtual e a sub-rede existem e se o Azure Batch tem acesso.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Este tipo de mensagem de erro pode ser assim: "Falhou em fornecer O Tempo de Integração em VNet. Se as definições do servidor DNS ou nsg estiverem configuradas, certifique-se de que o servidor DNS está acessível e o NSG está configurado corretamente."

Nesta situação, provavelmente tem uma configuração personalizada do servidor DNS ou definições do NSG, o que impede que o nome do servidor do Azure requerido pelo SSIS IR seja resolvido ou acedido. Para obter mais informações, veja [Configuração da Rede Virtual do SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se os problemas persistirem, contacte a equipa de suporte do Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

O SSIS IR será atualizado automaticamente de forma regular. Um novo conjunto do Azure Batch é criado durante a atualização e o conjunto do Azure Batch antigo é eliminado. Além disso, os recursos relacionados com a Rede Virtual do conjunto antigo são eliminados e os novos recursos relacionados com a Rede Virtual são criados na sua subscrição. Este erro significa que a eliminação de recursos relacionados com a Rede Virtual do conjunto antigo falhou devido a um bloqueio de eliminação ao nível da subscrição ou do grupo de recursos. Como o cliente controla e define o bloqueio de eliminação, o bloqueio de eliminação deve ser removido nesta situação.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Se o aprovisionamento do SSIS IR falhar, todos os recursos criados serão eliminados. No entanto, se existir um bloqueio de eliminação de recursos ao nível da subscrição ou do grupo de recursos, os recursos da Rede Virtual não serão eliminados conforme o esperado. Para corrigir este erro, remova o bloqueio de eliminação e reinicie o IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Quando parar o SSIS IR, todos os recursos relacionados com a Rede Virtual serão eliminados. Mas a eliminação poderá falhar se existir um bloqueio de eliminação de recursos ao nível da subscrição ou do grupo de recursos. Também aqui o cliente controla e define o bloqueio de eliminação. Portanto, o cliente deve remover o bloqueio de eliminação e parar o SSIS IR novamente.

### <a name="nodeunavailable"></a>NodeUnavailable

Este erro ocorre quando o IR está em execução e significa que o IR passou a estar em mau estado de funcionamento. Este erro é sempre causado por uma alteração no servidor DNS ou na configuração NSG que impede que o SSIS IR se ligue a um serviço necessário. Como a configuração do servidor DNS e do NSG é controlada pelo cliente, o cliente deve corrigir os problemas de bloqueio no seu lado. Para obter mais informações, veja [Configuração da Rede Virtual do SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Se os problemas persistirem, contacte a equipa de suporte do Azure Data Factory.

## <a name="static-public-ip-addresses-configuration"></a>Configuração de endereços IP públicos estáticos

Quando se junta ao IR Azure-SSIS à Rede Virtual Azure, também é possível trazer os seus próprios endereços IP públicos estáticos para o IR, de modo a que o IR possa aceder a fontes de dados que limitem o acesso a endereços IP específicos. Para obter mais informações, veja [Associar um Azure-SSIS Integration Runtime a uma rede virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Além dos problemas de rede virtuais acima, também pode conhecer questões estátáticas relacionadas com endereços IP públicos. Por favor, verifique se há ajuda nos seguintes erros.

### <a name="invalidpublicipspecified"></a><a name="InvalidPublicIPSpecified"></a>InválidoPublicIPEspecificado

Este erro pode ocorrer por uma variedade de razões quando iniciar o IR Azure-SSIS:

| Mensagem de erro | Solução|
|:--- |:--- |
| O endereço IP público estático fornecido já está utilizado, por favor forneça dois não utilizados para o seu Tempo de Execução de Integração Azure-SSIS. | Deve selecionar dois endereços IP públicos estáticos não utilizados ou remover as referências atuais ao endereço IP público especificado e, em seguida, reiniciar o IR Azure-SSIS. |
| O endereço IP público estático fornecido não tem nome DNS, por favor forneça dois deles com nome DNS para o seu Tempo de Execução de Integração Azure-SSIS. | Pode configurar o nome DNS do endereço IP público no portal Azure, como mostra a imagem abaixo. Os passos específicos são os seguintes: (1) Portal Open Azure e ir para a página de recursos deste endereço IP público; (2) Selecione a secção **de Configuração** e instale o nome DNS e, em seguida, clique no botão **Guardar;** (3) Reiniciar o seu IR Azure-SSIS. |
| O VNet fornecido e os endereços IP públicos estáticos para o seu Tempo de Execução de Integração Azure-SSIS devem estar no mesmo local. | De acordo com os requisitos da Rede Azure, o endereço IP público estático e a rede virtual devem estar no mesmo local e subscrição. Por favor, forneça dois endereços IP públicos estáticos válidos e reinicie o IR Azure-SSIS. |
| O endereço IP público estático fornecido é básico, por favor forneça dois padrão para o seu Tempo de Execução de Integração Azure-SSIS. | Consulte as [SKUs do Endereço IP Público](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) para obter ajuda. |

![Runtime de integração Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

### <a name="publicipresourcegrouplockedduringstart"></a>PublicIPResourceGroupLockedDuringStart

Se o fornecimento de IR Azure-SSIS falhar, todos os recursos criados são eliminados. No entanto, se houver um bloqueio de eliminação de recursos no nível de subscrição ou de recursos (que contém o seu endereço IP público estático), os recursos da rede não são eliminados como esperado. Para corrigir o erro, retire o bloqueio de eliminação e reinicie o IR.

### <a name="publicipresourcegrouplockedduringstop"></a>PublicIPResourceGroupLockedDuringStop

Quando parar o Ir Azure-SSIS, todos os recursos de rede criados no grupo de recursos que contêm o seu endereço IP público serão eliminados. Mas a eliminação pode falhar se houver um bloqueio de eliminação de recursos no nível de subscrição ou de recursos (que contém o seu endereço IP público estático). Retire o bloqueio de eliminação e reinicie o IR.

### <a name="publicipresourcegrouplockedduringupgrade"></a>PublicIPResourceGroupLockedDuringUpgrade

O IR Azure-SSIS é automaticamente atualizado regularmente. Novos nódosos de INFRAVERMELHOS são criados durante a atualização e os nódosos antigos serão eliminados. Além disso, os recursos de rede criados (por exemplo, o equilibrador de carga e o grupo de segurança da rede) para os nódosos antigos são eliminados, e os novos recursos de rede são criados sob a sua subscrição. Este erro significa que a eliminação dos recursos de rede para os nódosos antigos falhou devido a um bloqueio de exclusão no nível de subscrição ou de recursos (que contém o seu endereço IP público estático). Por favor, remova o bloqueio de eliminação para que possamos limpar os nós antigos e soltar o endereço IP público estático para os nós antigos. Caso contrário, o endereço IP público estático não pode ser lançado e não seremos capazes de atualizar ainda mais o seu IR.

### <a name="publicipnotusableduringupgrade"></a>PublicIPNotUsable DuringUpgrade

Quando quiser trazer os seus próprios endereços IP públicos estáticos, devem ser fornecidos dois endereços IP públicos. Um deles será usado para criar os nódosos de IR imediatamente e outro será usado durante a atualização do IR. Este erro pode ocorrer quando o outro endereço IP público não é utilizável durante a atualização. Consulte o [InvalidPublicIPEspecificado](#InvalidPublicIPSpecified) para possíveis causas.