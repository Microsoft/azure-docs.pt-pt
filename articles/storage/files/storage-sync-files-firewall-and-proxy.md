---
title: Definições de firewall e proxy do Ficheiro Azure no local / Microsoft Docs
description: Configuração da rede Azure File Sync no local
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dcf6160c3650975431bf50fcf5bcba67f833a717
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381227"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>As definições do proxy e da firewall do Azure File Sync
O Azure File Sync liga os seus servidores no local aos Ficheiros Azure, permitindo a sincronização multi-site e as funcionalidades de tiering em nuvem. Como tal, um servidor no local deve ser ligado à internet. Um administrador de TI precisa decidir o melhor caminho para o servidor chegar aos serviços de nuvem Azure.

Este artigo fornecerá informações sobre requisitos e opções específicas disponíveis para ligar com sucesso e de forma segura o seu servidor ao Azure File Sync.

## <a name="overview"></a>Descrição geral
O Azure File Sync funciona como um serviço de orquestração entre o seu Windows Server, a sua partilha de ficheiros Azure e vários outros serviços Azure para sincronizar dados conforme descrito no seu grupo de sincronização. Para que o Azure File Sync funcione corretamente, terá de configurar os seus servidores para comunicar com os seguintes serviços Azure:

- Storage do Azure
- Azure File Sync
- Azure Resource Manager
- Serviços de autenticação

> [!Note]  
> O agente Dessincronização de ficheiros Azure no Windows Server inicia todos os pedidos de serviços na nuvem, o que resulta em apenas ter de considerar o tráfego de saída a partir de uma perspetiva de firewall. <br /> Nenhum serviço Azure inicia uma ligação com o agente Dessincronização de Ficheiros Azure.

## <a name="ports"></a>Portas
O Azure File Sync move dados de ficheiros e metadados exclusivamente sobre HTTPS e requer que a porta 443 seja aberta.
Como resultado, todo o tráfego está encriptado.

## <a name="networks-and-special-connections-to-azure"></a>Redes e ligações especiais ao Azure
O agente Dessincronização de Ficheiros Azure não tem requisitos em relação a canais especiais como [expressRoute](../../expressroute/expressroute-introduction.md), etc. para o Azure.

O Azure File Sync funcionará através de todos os meios disponíveis que permitam chegar ao Azure, adaptando-se automaticamente a várias características da rede, como largura de banda, latência e oferta de controlo administrativo para afinação. Nem todas as funcionalidades estão disponíveis neste momento. Se quiser configurar comportamentos específicos, avise-nos através do [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
O Azure File Sync suporta definições específicas de procuração específicas e de procuração em toda a máquina.

**As definições de proxy específicas** da aplicação permitem a configuração de um proxy especificamente para o tráfego de Sincronização de Ficheiros Azure. As definições de proxy específicas da aplicação são suportadas na versão 4.0.1.0 ou mais recente do agente e podem ser configuradas durante a instalação do agente ou utilizando o cmdlet de Configuração PowerShell de Configuração De Configuração De Configuração De Configuração De Configuração De Conjunto-ArmazenamentoSyncProxy.

PowerShell ordena configurar definições de proxy específicas da aplicação:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**As definições de procuração** em toda a máquina são transparentes para o agente Dessincronização de ficheiros Azure, uma vez que todo o tráfego do servidor é encaminhado através do proxy.

Para configurar as definições de procuração em toda a máquina, siga os passos abaixo: 

1. Configure as definições de procuração para aplicações .NET 

   - Editar estes dois ficheiros:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Adicione a secção <system.net> secção nos ficheiros machine.config (abaixo da secção <system.serviceModel> ).  Altere 127.0.01:8888 para o endereço IP e porta para o servidor proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Definir as definições de proxy WinHTTP 

   - Executar o seguinte comando a partir de um pedido de comando elevado ou PowerShell para ver a definição de procuração existente:   

     netsh winhttp mostrar procuração

   - Executar o seguinte comando a partir de um pedido de comando elevado ou PowerShell para definir a definição de procuração (alteração 127.01:8888 para o endereço IP e porta para o servidor proxy):  

     netsh winhttp set proxy 127.0.0.1:8888

3. Reiniciar o serviço de Agente de Sincronização de Armazenamento executando o seguinte comando a partir de um pedido de comando elevado ou PowerShell: 

      net stop filesyncsvc

      Nota: O serviço de Agente de Sincronização de Armazenamento (filesyncsvc) iniciará automaticamente uma vez parado.

## <a name="firewall"></a>Firewall
Como referido numa secção anterior, o porto 443 tem de ser aberto. Com base em políticas no seu datacenter, sucursal ou região, pode ser desejado ou necessário um maior restrição do tráfego sobre este porto para domínios específicos.

A tabela seguinte descreve os domínios necessários para a comunicação:

| Serviço | Ponto final da nuvem pública | Ponto final do Governo de Azure | Utilização |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Qualquer chamada de utilizador (como powerShell) vai para/através deste URL, incluindo a chamada inicial de registo do servidor. |
| **Azure Active Directory** | https://login.windows.net<br>https://login.microsoftonline.com | https://login.microsoftonline.us | As chamadas do Gestor de Recursos Azure devem ser feitas por um utilizador autenticado. Para ter sucesso, este URL é utilizado para a autenticação do utilizador. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Como parte da implementação do Azure File Sync, será criado um diretor de serviço no Diretório Ativo Azure da subscrição. Este URL é usado para isso. Este principal é utilizado para delegar um conjunto mínimo de direitos ao serviço Desincronização de Ficheiros Azure. O utilizador que executa a configuração inicial do Azure File Sync deve ser um utilizador autenticado com privilégios do proprietário da subscrição. |
| **Armazenamento do Azure** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Quando o servidor descarrega um ficheiro, então o servidor executa esse movimento de dados de forma mais eficiente quando fala diretamente com a parte de ficheiro Azure na Conta de Armazenamento. O servidor tem uma chave SAS que só permite o acesso à partilha de ficheiros direcionado. |
| **Azure File Sync** | &ast;.one.microsoft.com<br>&ast;.afs.azure.net | &ast;.afs.azure.us | Após o registo inicial do servidor, o servidor recebe um URL regional para a instância de serviço Azure File Sync naquela região. O servidor pode utilizar o URL para comunicar direta e eficientemente com a instância que manuseia a sua sincronização. |
| **Microsoft PKI** | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | https://www.microsoft.com/pki/mscorp/cps<br><http://ocsp.msocsp.com> | Uma vez instalado o agente Bluee File Sync, o URL PKI é utilizado para descarregar certificados intermédios necessários para comunicar com o serviço Desincronização de Ficheiros Azure e a partilha de ficheiros Azure. O URL OCSP é utilizado para verificar o estado de um certificado. |

> [!Important]
> Ao permitir que o tráfego &ast;.one.microsoft.com, o tráfego para mais do que apenas o serviço de sincronização é possível a partir do servidor. Existem muitos mais serviços da Microsoft disponíveis em subdomínios.

Se &ast;.one.microsoft.com é demasiado ampla, pode limitar a comunicação do servidor, permitindo a comunicação apenas a instâncias regionais explícitas do serviço Azure Files Sync. Que instância(s) escolher depende da região do serviço de sincronização de armazenamento que implementou e registou o servidor. Esta região chama-se "URL de ponto final primário" na tabela abaixo.

Por razões de continuidade de negócios e recuperação de desastres (BCDR), pode ter especificado as suas ações de ficheiro Azure numa conta de armazenamento globalmente redundante (GRS). Se for esse o caso, então as suas ações de ficheiro Saque A partir de Azure falhará na região emparelhada em caso de uma paragem regional duradoura. O Azure File Sync utiliza os mesmos pares regionais que o armazenamento. Por isso, se utilizar contas de armazenamento GRS, tem de ativar URLs adicionais para permitir que o seu servidor fale com a região emparelhada para o Azure File Sync. A tabela abaixo chama a esta "região emparelhada". Além disso, existe um URL de perfil de gestor de tráfego que precisa ser ativado também. Isto garantirá que o tráfego da rede pode ser perfeitamente reencaminhado para a região emparelhada em caso de falha e é chamado de "Discovery URL" na tabela abaixo.

| Cloud  | Região | URL de ponto final primário | Região emparelhada | URL de descoberta |
|--------|--------|----------------------|---------------|---------------|
| Público |Leste da Austrália | https:\//kailani-aue.one.microsoft.com | Austrália Sudeste | https:\//tm-kailani-aue.one.microsoft.com |
| Público |Austrália Sudeste | https:\//kailani-aus.one.microsoft.com | Leste da Austrália | https:\//tm-kailani-aus.one.microsoft.com |
| Público | Sul do Brasil | https:\//brazilsouth01.afs.azure.net | E.U.A. Centro-Sul | https:\//tm-brazilsouth01.afs.azure.net |
| Público | Canadá Central | https:\//kailani-cac.one.microsoft.com | Leste do Canadá | https:\//tm-kailani-cac.one.microsoft.com |
| Público | Leste do Canadá | https:\//kailani-cae.one.microsoft.com | Canadá Central | https:\//tm-kailani.cae.one.microsoft.com |
| Público | Índia Central | https:\//kailani-cin.one.microsoft.com | Sul da Índia | https:\//tm-kailani-cin.one.microsoft.com |
| Público | E.U.A. Central | https:\//kailani-cus.one.microsoft.com | E.U.A. Leste 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Público | Ásia Leste | https:\//kailani11.one.microsoft.com | Ásia Sudeste | https:\//tm-kailani11.one.microsoft.com |
| Público | E.U.A. Leste | https:\//kailani1.one.microsoft.com | E.U.A. Oeste | https:\//tm-kailani1.one.microsoft.com |
| Público | E.U.A. Leste 2 | https:\//kailani-ess.one.microsoft.com | E.U.A. Central | https:\//tm-kailani-ess.one.microsoft.com |
| Público | Leste do Japão | https:\//japaneast01.afs.azure.net | Oeste do Japão | https:\//tm-japaneast01.afs.azure.net |
| Público | Oeste do Japão | https:\//japanwest01.afs.azure.net | Leste do Japão | https:\//tm-japanwest01.afs.azure.net |
| Público | Coreia do Sul Central | https:\//koreacentral01.afs.azure.net/ | Sul da Coreia do Sul | https:\//tm-koreacentral01.afs.azure.net/ |
| Público | Sul da Coreia do Sul | https:\//koreasouth01.afs.azure.net/ | Coreia do Sul Central | https:\//tm-koreasouth01.afs.azure.net/ |
| Público | E.U.A. Centro-Norte | https:\//northcentralus01.afs.azure.net | E.U.A. Centro-Sul | https:\//tm-northcentralus01.afs.azure.net |
| Público | Europa do Norte | https:\//kailani7.one.microsoft.com | Europa Ocidental | https:\//tm-kailani7.one.microsoft.com |
| Público | E.U.A. Centro-Sul | https:\//southcentralus01.afs.azure.net | E.U.A. Centro-Norte | https:\//tm-southcentralus01.afs.azure.net |
| Público | Sul da Índia | https:\//kailani-sin.one.microsoft.com | Índia Central | https:\//tm-kailani-sin.one.microsoft.com |
| Público | Ásia Sudeste | https:\//kailani10.one.microsoft.com | Ásia Leste | https:\//tm-kailani10.one.microsoft.com |
| Público | Sul do Reino Unido | https:\//kailani-uks.one.microsoft.com | Oeste do Reino Unido | https:\//tm-kailani-uks.one.microsoft.com |
| Público | Oeste do Reino Unido | https:\//kailani-ukw.one.microsoft.com | Sul do Reino Unido | https:\//tm-kailani-ukw.one.microsoft.com |
| Público | E.U.A. Centro-Oeste | https:\//westcentralus01.afs.azure.net | E.U.A.Oeste 2 | https:\//tm-westcentralus01.afs.azure.net |
| Público | Europa Ocidental | https:\//kailani6.one.microsoft.com | Europa do Norte | https:\//tm-kailani6.one.microsoft.com |
| Público | E.U.A. Oeste | https:\//kailani.one.microsoft.com | E.U.A. Leste | https:\//tm-kailani.one.microsoft.com |
| Público | E.U.A.Oeste 2 | https:\//westus201.afs.azure.net | E.U.A. Centro-Oeste | https:\//tm-westus201.afs.azure.net |
| Governo | US Gov - Arizona | https:\//usgovarizona01.afs.azure.us | US Gov - Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Governo | US Gov - Texas | https:\//usgovtexas01.afs.azure.us | US Gov - Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Se utilizar contas de armazenamento redundantes localmente redundantes (LRS) ou de zona redundante (ZRS), apenas necessita de ativar o URL listado em "URL de ponto final primário".

- Se utilizar contas de armazenamento globalmente redundantes (GRS), ative três URLs.

**Exemplo:** Implementa um serviço de sincronização de armazenamento em `"West US"` e regista o seu servidor com ele. Os URLs para permitir que o servidor se comunique para este caso são:

> - https:\//kailani.one.microsoft.com (ponto final primário: Oeste dos EUA)
> - https:\//kailani1.one.microsoft.com (região de fail-over emparelhada: Leste dos EUA)
> - https:\//tm-kailani.one.microsoft.com (URL de descoberta da região primária)

## <a name="test-network-connectivity-to-service-endpoints"></a>Testar a conectividade da rede aos pontos finais do serviço
Uma vez registado um servidor com o serviço Desincronização de Ficheiros Azure, o Cmdlet de Conectividade Test-StorageSyncNetworkE e serverRegistration.exe podem ser utilizados para testar comunicações com todos os pontos finais (URLs) específicos deste servidor. Este cmdlet pode ajudar a resolver problemas quando uma comunicação incompleta impede o servidor de trabalhar totalmente com o Azure File Sync e pode ser usado para afinar configurações de proxy e firewall.

Para executar o teste de conectividade da rede, instale a versão 9.1 ou posterior do agente Dessincronizado do Ficheiro Azure e execute os seguintes comandos PowerShell:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Test-StorageSyncNetworkConnectivity
```

## <a name="summary-and-risk-limitation"></a>Limitação do resumo e do risco
As listas anteriores neste documento contêm o URLs Azure File Sync com que comunica atualmente. As firewalls devem ser capazes de permitir a saída do tráfego para estes domínios. A Microsoft esforça-se por manter esta lista atualizada.

A criação de regras de firewall de restrição de domínio pode ser uma medida para melhorar a segurança. Se estas configurações de firewall forem utilizadas, é preciso ter em mente que os URLs serão adicionados e podem até mudar ao longo do tempo. Verifique este artigo periodicamente.

## <a name="next-steps"></a>Passos seguintes
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
