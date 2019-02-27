---
title: O Azure File Sync no local as definições de firewall e proxy | Documentos da Microsoft
description: O Azure File Sync no local a configuração de rede
services: storage
author: fauhse
ms.service: storage
ms.topic: article
ms.date: 11/26/2018
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 23bf70cd60639aec3ea7e8504dc3f6ebccd4923f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883594"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>As definições do proxy e da firewall do Azure File Sync
O Azure File Sync liga-se os servidores no local para os ficheiros do Azure, ativar a sincronização de múltiplos site e recursos de camada de cloud. Como tal, um servidor no local tem de estar ligado à internet. Administrador de TI tem de decidir o melhor caminho para o servidor chegar a serviços cloud do Azure.

Este artigo fornece informações sobre os requisitos específicos e as opções disponíveis para a ligação com êxito e segura de seu servidor para o Azure File Sync.

> [!Important]
> O Azure File Sync ainda não suporta firewalls e redes virtuais para uma conta de armazenamento.

## <a name="overview"></a>Descrição geral
O Azure File Sync atua como um serviço de orquestração entre o Windows Server, a partilha de ficheiros e vários outros serviços do Azure para sincronizar os dados, tal como descrito no seu grupo de sincronização. Para o Azure File Sync funcione corretamente, terá de configurar os servidores para comunicar com os serviços do Azure seguintes:

- Storage do Azure
- Azure File Sync
- Azure Resource Manager
- Serviços de autenticação

> [!Note]  
> O agente de sincronização de ficheiros do Azure no Windows Server inicia todos os pedidos para a cloud services que resulta em apenas ter a ter em consideração o tráfego de saída de uma perspectiva de firewall. <br /> Nenhum serviço do Azure inicia uma ligação ao agente do Azure File Sync.

## <a name="ports"></a>Portas
O Azure File Sync move dados de ficheiros e metadados exclusivamente através de HTTPS e requer a porta 443 para ser abrir saída.
Como resultado, todo o tráfego é encriptado.

## <a name="networks-and-special-connections-to-azure"></a>Redes e ligações especiais para o Azure
O agente de sincronização de ficheiros do Azure não tem requisitos sobre canais especiais, como [ExpressRoute](../../expressroute/expressroute-introduction.md), etc. para o Azure.

O Azure File Sync irá funcionar através de meios que disponíveis que permitem o alcance para o Azure, automaticamente adaptação de várias características de rede, como a largura de banda, latência, bem como oferta de controlo de administração para ajuste fino. Nem todas as funcionalidades estão disponíveis neste momento. Se quiser configurar o comportamento específico, fale conosco [UserVoice de ficheiros do Azure](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
O Azure File Sync suporta as definições de proxy de aplicações específicas e todo o computador.

**Definições de proxy de aplicações específicas** permitem a configuração de um proxy especificamente para o tráfego do Azure File Sync. Definições de proxy de aplicações específicas são suportadas na versão do agente 4.0.1.0 ou mais recente e podem ser configuradas durante a instalação do agente ou utilizando o cmdlet do PowerShell Set-StorageSyncProxyConfiguration.

Comandos do PowerShell para configurar definições de proxy de aplicações específicas:
```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**Todo o computador as definições de proxy** são transparentes para o agente de sincronização de ficheiros do Azure como o tráfego inteiro do servidor é encaminhado através do proxy.

Para configurar as definições de proxy de todo o computador, siga os passos abaixo: 

1. Configurar definições de proxy para aplicações .NET 

  - Edite esses dois arquivos:  
    C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
    C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

  - Adicione a secção de < system.net > nos arquivos Machine. config (abaixo da seção < System. ServiceModel >).  Alterar 127.0.01:8888 para o endereço IP e porta do servidor proxy. 
  ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
  ```

2. Configurar as definições de proxy de WinHTTP 

  - Execute o seguinte comando a partir de uma linha de comandos elevada ou o PowerShell para ver a definição de proxy existente:   

    proxy do Netsh winhttp show

  - Execute o seguinte comando a partir de uma linha de comandos elevada ou o PowerShell para definir a definição de proxy (alterar 127.0.01:8888 para o endereço IP e porta do servidor proxy):  

    netsh winhttp define 127.0.0.1:8888 de proxy

3. Reinicie o serviço de agente de sincronização de armazenamento, executando o seguinte comando a partir de uma linha de comandos elevada ou o PowerShell: 

      net stop filesyncsvc

      Nota: O serviço de agente de sincronização de armazenamento (filesyncsvc) será início automático depois de parado.

## <a name="firewall"></a>Firewall
Conforme mencionado na secção anterior, 443 às necessidades de porta a ser abra saída. Com base em políticas no seu datacenter, o ramo ou a região, restringir ainda mais o tráfego através desta porta a domínios específicos pode ser pretendido ou necessário.

A tabela seguinte descreve os domínios necessários para a comunicação:

| Serviço | Ponto final de cloud pública | Ponto final de administração pública do Azure | Utilização |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Qualquer chamada de usuário (como o PowerShell) passa para/por este URL, incluindo a chamada de registo do servidor inicial. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | Chamadas de Gestor de recursos do Azure têm de ser feitas por um usuário autenticado. Para ter êxito, este URL é utilizado para autenticação de utilizador. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Como parte da implementação do Azure File Sync, será criado um principal de serviço no Azure Active Directory da subscrição. Este URL é usado para isso. Este principal é utilizado para delegar a um conjunto mínimo de direitos para o serviço de sincronização de ficheiros do Azure. O utilizador que executa a configuração inicial do Azure File Sync tem de ser um utilizador autenticado com privilégios de proprietário da subscrição. |
| **Armazenamento do Azure** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Quando o servidor transfere um ficheiro, em seguida, o servidor executa esse movimento de dados com mais eficiência quando se fala diretamente para a partilha de ficheiros do Azure na conta de armazenamento. O servidor tem uma chave SAS que só permite o acesso de partilha de ficheiros de destino. |
| **Azure File Sync** | &ast;.one.microsoft.com | &ast;.afs.azure.us | Após o registo do servidor inicial, o servidor recebe um URL regional para a instância do serviço Azure File Sync nessa região. O servidor pode utilizar o URL para se comunicar diretamente e eficiente com a instância de lidar com a sincronizar. |
| **Microsoft PKI** | `https://www.microsoft.com/pki/mscorp`<br />http://ocsp.msocsp.com | `https://www.microsoft.com/pki/mscorp`<br />http://ocsp.msocsp.com | Depois de instalar o agente de sincronização de ficheiros do Azure, o URL de PKI é utilizado para transferir os certificados intermédios necessários para comunicar com o serviço de sincronização de ficheiros do Azure e a partilha de ficheiros do Azure. O URL de protocolo OCSP é utilizado para verificar o estado de um certificado. |

> [!Important]
> Quando a permitir o tráfego para &ast;. one.microsoft.com, o tráfego para mais do que apenas o serviço de sincronização é possível a partir do servidor. Existem muitos serviços da Microsoft mais disponíveis em subdomínios.

Se &ast;. one.microsoft.com é demasiado abrangente, pode limitar a comunicação do servidor ao permitir que a comunicação com instâncias de regionais apenas explícitas do serviço Azure File Sync. Qual instância (s) para escolher depende da região do serviço de sincronização de armazenamento ter implementado e o servidor registado. Essa região é chamada "URL do ponto final primário" na tabela abaixo.

Por motivos de (BCDR) de recuperação de desastres e continuidade do negócio pode ter especificado que os ficheiros do Azure partilham numa conta de armazenamento globalmente redundante (GRS). Se for esse o caso, em seguida, as partilhas de ficheiros do Azure irão efetuar a ativação pós-falha para a região emparelhada em caso de uma falha regional duradouras. O Azure File Sync utiliza os mesmo emparelhamentos regionais como armazenamento. Portanto, se utilizar contas de armazenamento GRS, tem de ativar a URLs adicionais permitir que o seu servidor para comunicar com a região emparelhada para o Azure File Sync. A tabela a seguir chama nesta região"Paired". Além disso, existe um URL de perfil do Gestor de tráfego que tem de ser ativada também. Isto irá garantir que pode ser perfeitamente reencaminhado para a região emparelhada em caso de uma ativação pós-falha de tráfego de rede e é chamado "Deteção URL" na tabela abaixo.

| Nuvem  | Região | URL de ponto final primário | Região emparelhada | URL de Deteção |
|--------|--------|----------------------|---------------|---------------|
| Público |Leste da Austrália | https://kailani-aue.one.microsoft.com | Sudeste da Austrália | https://kailani-aue.one.microsoft.com |
| Público |Sudeste da Austrália | https://kailani-aus.one.microsoft.com | Leste da Austrália | https://tm-kailani-aus.one.microsoft.com |
| Público | Canadá Central | https://kailani-cac.one.microsoft.com | Leste do Canadá | https://tm-kailani-cac.one.microsoft.com |
| Público | Leste do Canadá | https://kailani-cae.one.microsoft.com | Canadá Central | https://tm-kailani.cae.one.microsoft.com |
| Público | EUA Central | https://kailani-cus.one.microsoft.com | EUA Leste 2 | https://tm-kailani-cus.one.microsoft.com |
| Público | Ásia Oriental | https://kailani11.one.microsoft.com | Sudeste Asiático | https://tm-kailani11.one.microsoft.com |
| Público | EUA Leste | https://kailani1.one.microsoft.com | EUA Oeste | https://tm-kailani1.one.microsoft.com |
| Público | EUA Leste 2 | https://kailani-ess.one.microsoft.com | EUA Central | https://tm-kailani-ess.one.microsoft.com |
| Público | Europa do Norte | https://kailani7.one.microsoft.com | Europa Ocidental | https://tm-kailani7.one.microsoft.com |
| Público | Sudeste Asiático | https://kailani10.one.microsoft.com | Ásia Oriental | https://tm-kailani10.one.microsoft.com |
| Público | Reino Unido Sul | https://kailani-uks.one.microsoft.com | Reino Unido Oeste | https://tm-kailani-uks.one.microsoft.com |
| Público | Reino Unido Oeste | https://kailani-ukw.one.microsoft.com | Reino Unido Sul | https://tm-kailani-ukw.one.microsoft.com |
| Público | Europa Ocidental | https://kailani6.one.microsoft.com | Europa do Norte | https://tm-kailani6.one.microsoft.com |
| Público | EUA Oeste | https://kailani.one.microsoft.com | EUA Leste | https://tm-kailani.one.microsoft.com |
| Governo | Gov (US) - Arizona | https://usgovarizona01.afs.azure.us | Gov (US) - Texas | https://tm-usgovarizona01.afs.azure.us |
| Governo | Gov (US) - Texas | https://usgovtexas01.afs.azure.us | Gov (US) - Arizona | https://tm-usgovtexas01.afs.azure.us |

- Se usar localmente redundante (LRS) ou contas de armazenamento (ZRS) com redundância de zona, só precisa de ativar o URL listado em "URL do ponto final primário".

- Se utilizar contas de armazenamento globalmente redundantes (GRS), ative três URLs.

**Exemplo:** Implementar um serviço de sincronização de armazenamento no `"West US"` e registar o servidor com o mesmo. Os URLs para permitir que o servidor comunicar com o neste caso, são:

> - https://kailani.one.microsoft.com (ponto final primário: Oeste dos E.U.A.)
> - https://kailani1.one.microsoft.com (região emparelhada de ativação pós-falha: Este dos E.U.A.)
> - https://tm-kailani.one.microsoft.com (URL de deteção da região primária)

## <a name="summary-and-risk-limitation"></a>Resumo e o risco de limitação
As listas anteriormente este documento contém os URLs do Azure File Sync atualmente se comunica com. Firewalls tem de ser capazes de permitir o tráfego de saída para esses domínios. A Microsoft está empenhada manter esta lista atualizada.

Configuração de domínio, restringindo as regras de firewall pode ser uma medida para melhorar a segurança. Se forem utilizadas estas configurações de firewall, é necessário ter em mente que URLs serão adicionados e poderão até mesmo alterar ao longo do tempo. Consulte este artigo periodicamente.

## <a name="next-steps"></a>Passos Seguintes
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorizar a sincronização de ficheiros do Azure](storage-sync-files-monitoring.md)
