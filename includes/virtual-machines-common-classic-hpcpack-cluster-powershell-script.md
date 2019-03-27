---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 6f0d2d59ed50c743adb19027c404bfa83a1886f1
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484868"
---
Dependendo do seu ambiente e opções, o script pode criar toda a infraestrutura de cluster, incluindo a rede virtual do Azure, as contas de armazenamento, serviços cloud, controlador de domínio, bases de dados locais ou remotos de SQL, nó principal e nós do cluster adicional. Em alternativa, o script pode utilizar a infraestrutura do Azure já existente e criar apenas os nós de cluster HPC.

Para obter informações gerais sobre o planeamento de um cluster HPC Pack, consulte a [avaliação do produto e de planeamento](https://technet.microsoft.com/library/jj899596.aspx) e [introdução](https://technet.microsoft.com/library/jj899590.aspx) conteúdo na biblioteca do TechNet HPC Pack 2012 R2.

## <a name="prerequisites"></a>Pré-requisitos
* **Subscrição do Azure**: Pode utilizar uma subscrição no serviço do Azure Global ou do Azure China. Os limites de subscrição afetam o número e tipo de nós de cluster, que pode implementar. Para obter informações, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../articles/azure-subscription-service-limits.md).
* **Computador de cliente do Windows com o Azure PowerShell 0.8.10 ou posterior instalado e configurado**: Ver [introdução ao Azure PowerShell](/powershell/azureps-cmdlets-docs) para instruções de instalação e os passos ligar à sua subscrição do Azure.
* **Script de implementação do HPC Pack IaaS**: Baixe e Descompacte a versão mais recente do script a partir da [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `New-HPCIaaSCluster.ps1 –Version`. Este artigo baseia-se a versão 4.5.2 do script.
* **Ficheiro de configuração de script**: Crie um ficheiro XML que o script usa para configurar o cluster HPC. Para informações e exemplos, consulte as secções mais adiante neste artigo e o ficheiro Manual.rtf que acompanha o script de implementação.

## <a name="syntax"></a>Sintaxe
```powershell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> Execute o script como um administrador.
> 
> 

### <a name="parameters"></a>Parâmetros
* **ConfigFile**: Especifica o caminho do ficheiro de configuração para descrever o cluster HPC. Ver mais informações sobre o ficheiro de configuração neste tópico, ou no ficheiro Manual.rtf na pasta que contém o script.
* **AdminUserName**: Especifica o nome de utilizador. Se a floresta de domínio é criada pelo script, isso se torna o nome de utilizador de administrador local para todas as VMs e o nome de administrador de domínio. Se a floresta de domínio já existir, esta ação Especifica o utilizador de domínio como o nome de utilizador de administrador local para instalar o HPC Pack.
* **AdminPassword**: Especifica a senha do administrador. Se não for especificado na linha de comandos, o script pede-lhe introduzir a palavra-passe.
* **HPCImageName** (opcional): Especifica o nome de imagem de VM do HPC Pack utilizado para implementar o cluster HPC. Tem de ser uma imagem fornecida pelo Microsoft HPC Pack no Azure Marketplace. Se não for especificado (recomendado normalmente), o script escolhe as mais recentes publicados [HPC Pack 2012 R2 imagem](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/). A imagem mais recente é baseada no Windows Server 2012 R2 Datacenter com o HPC Pack 2012 R2 Update 3 instalado.
  
  > [!NOTE]
  > Falha na implementação se não especificar uma imagem do HPC Pack válida.
  > 
  > 
* **Ficheiro de registo** (opcional): Especifica o caminho de ficheiro de registo de implementação. Se não for especificado, o script cria um ficheiro de registo no diretório temporário do computador a executar o script.
* **Força** (opcional): Suprime a todos os pedidos de confirmação.
* **NoCleanOnFailure** (opcional): Especifica que não são removidas as VMs do Azure que não são implementadas com êxito. Remova manualmente estas VMs antes de executar novamente o script para continuar que a implementação ou a implementação poderá falhar.
* **PSSessionSkipCACheck** (optional): Para cada serviço cloud com as VMs implementadas por este script, um certificado autoassinado é gerado automaticamente pelo Azure e todas as VMs no serviço cloud utilizam este certificado como o certificado de gestão remota do Windows (WinRM) predefinido. Para implementar recursos do HPC nestas VMs do Azure, o script por predefinição instala temporariamente estes certificados no computador Local\\arquivo de autoridades de certificação de raiz fidedigna do computador cliente para suprimir o erro de segurança de "autoridade de certificação não confiável" durante a execução do script. Os certificados são removidos quando o script termina. Se este parâmetro for especificado, os certificados não estão instalados no computador cliente e o aviso de segurança suprimido.
  
  > [!IMPORTANT]
  > Este parâmetro não é recomendado para implementações de produção.
  > 
  > 

### <a name="example"></a>Exemplo
O exemplo seguinte cria um cluster HPC Pack através do ficheiro de configuração *MyConfigFile.xml*e especifica as credenciais de administrador para instalar o cluster.

```powershell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Considerações adicionais
* O script, opcionalmente, pode ativar a submissão de tarefas através do portal web do HPC Pack ou a API de REST do HPC Pack.
* O script, opcionalmente, pode executar os scripts de pré e pós-configuração personalizados no nó principal se pretender instalar software adicional ou configurar outras definições.

## <a name="configuration-file"></a>Ficheiro de configuração
O ficheiro de configuração para o script de implementação é um arquivo XML. O ficheiro de esquema HPCIaaSClusterConfig.xsd está na pasta de script de implementação do HPC Pack IaaS. **IaaSClusterConfig** é o elemento de raiz do ficheiro de configuração, que contém os elementos filho descritos em detalhe no ficheiro Manual.rtf na pasta de script de implementação.

