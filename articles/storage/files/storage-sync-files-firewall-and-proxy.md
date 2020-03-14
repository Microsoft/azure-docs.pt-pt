---
title: Definições de firewall e proxy do Ficheiro Azure no local / Microsoft Docs
description: Configuração da rede Azure File Sync no local
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 93681813c12f0df99909c849e57153e7a64c78fb
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299316"
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

| Service | Ponto final da nuvem pública | Ponto final do Governo de Azure | Utilização |
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

| Nuvem  | Região | URL de ponto final primário | Região emparelhada | URL de descoberta |
|--------|--------|----------------------|---------------|---------------|
| Público |Leste da Austrália | https:\//kailani-aue.one.microsoft.com | Sudeste da Austrália | https:\//tm-kailani-aue.one.microsoft.com |
| Público |Sudeste da Austrália | https:\//kailani-aus.one.microsoft.com | Leste da Austrália | https:\//tm-kailani-aus.one.microsoft.com |
| Público | Sul do Brasil | https:\//brazilsouth01.afs.azure.net | EUA Centro-Sul | https:\//tm-brazilsouth01.afs.azure.net |
| Público | Canadá Central | https:\//kailani-cac.one.microsoft.com | Leste do Canadá | https:\//tm-kailani-cac.one.microsoft.com |
| Público | Leste do Canadá | https:\//kailani-cae.one.microsoft.com | Canadá Central | https:\//tm-kailani.cae.one.microsoft.com |
| Público | Índia Central | https:\//kailani-cin.one.microsoft.com | Sul da Índia | https:\//tm-kailani-cin.one.microsoft.com |
| Público | EUA Central | https:\//kailani-cus.one.microsoft.com | EUA Leste 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Público | Ásia Oriental | https:\//kailani11.one.microsoft.com | Sudeste Asiático | https:\//tm-kailani11.one.microsoft.com |
| Público | EUA Leste | https:\//kailani1.one.microsoft.com | EUA Oeste | https:\//tm-kailani1.one.microsoft.com |
| Público | EUA Leste 2 | https:\//kailani-ess.one.microsoft.com | EUA Central | https:\//tm-kailani-ess.one.microsoft.com |
| Público | Leste do Japão | https:\//japaneast01.afs.azure.net | Oeste do Japão | https:\//tm-japaneast01.afs.azure.net |
| Público | Oeste do Japão | https:\//japanwest01.afs.azure.net | Leste do Japão | https:\//tm-japanwest01.afs.azure.net |
| Público | Coreia do Sul Central | https:\//koreacentral01.afs.azure.net/ | Coreia do Sul | https:\//tm-koreacentral01.afs.azure.net/ |
| Público | Coreia do Sul | https:\//koreasouth01.afs.azure.net/ | Coreia do Sul Central | https:\//tm-koreasouth01.afs.azure.net/ |
| Público | EUA Centro-Norte | https:\//northcentralus01.afs.azure.net | EUA Centro-Sul | https:\//tm-northcentralus01.afs.azure.net |
| Público | Europa do Norte | https:\//kailani7.one.microsoft.com | Europa Ocidental | https:\//tm-kailani7.one.microsoft.com |
| Público | EUA Centro-Sul | https:\//southcentralus01.afs.azure.net | EUA Centro-Norte | https:\//tm-southcentralus01.afs.azure.net |
| Público | Sul da Índia | https:\//kailani-sin.one.microsoft.com | Índia Central | https:\//tm-kailani-sin.one.microsoft.com |
| Público | Sudeste Asiático | https:\//kailani10.one.microsoft.com | Ásia Oriental | https:\//tm-kailani10.one.microsoft.com |
| Público | Reino Unido Sul | https:\//kailani-uks.one.microsoft.com | Reino Unido Oeste | https:\//tm-kailani-uks.one.microsoft.com |
| Público | Reino Unido Oeste | https:\//kailani-ukw.one.microsoft.com | Reino Unido Sul | https:\//tm-kailani-ukw.one.microsoft.com |
| Público | EUA Centro-Oeste | https:\//westcentralus01.afs.azure.net | EUA Oeste 2 | https:\//tm-westcentralus01.afs.azure.net |
| Público | Europa Ocidental | https:\//kailani6.one.microsoft.com | Europa do Norte | https:\//tm-kailani6.one.microsoft.com |
| Público | EUA Oeste | https:\//kailani.one.microsoft.com | EUA Leste | https:\//tm-kailani.one.microsoft.com |
| Público | EUA Oeste 2 | https:\//westus201.afs.azure.net | EUA Centro-Oeste | https:\//tm-westus201.afs.azure.net |
| Governo | Gov (US) - Arizona | https:\//usgovarizona01.afs.azure.us | Gov (US) - Texas | https:\//tm-usgovarizona01.afs.azure.us |
| Governo | Gov (US) - Texas | https:\//usgovtexas01.afs.azure.us | Gov (US) - Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Se utilizar contas de armazenamento redundantes localmente redundantes (LRS) ou de zona redundante (ZRS), apenas necessita de ativar o URL listado em "URL de ponto final primário".

- Se utilizar contas de armazenamento globalmente redundantes (GRS), ative três URLs.

**Exemplo:** Implementa um serviço de sincronização de armazenamento em `"West US"` e regista o seu servidor com ele. Os URLs para permitir que o servidor se comunique para este caso são:

> - https:\//kailani.one.microsoft.com (ponto final primário: Oeste dos EUA)
> - https:\//kailani1.one.microsoft.com (região de fail-over emparelhada: Leste dos EUA)
> - https:\//tm-kailani.one.microsoft.com (URL de descoberta da região primária)

### <a name="allow-list-for-azure-file-sync-ip-addresses"></a>Permitir lista para endereços IP de Sincronização de Ficheiros Azure
Se a firewall no local necessitar de adicionar endereços IP específicos a uma lista de permitir a ligação ao Azure File Sync, pode adicionar as seguintes gamas de endereços IP com base nas regiões a que está a ligar.

| Região | Intervalos de endereços IP |
|--------|-------------------|
| EUA Central | 52.176.149.179/32, 20.37.157.80/29 |
| EUA Leste 2 | 40.123.47.110/32, 20.41.5.144/29 |
| EUA Leste | 104.41.148.238/32, 20.42.4.248/29 |
| EUA Centro-Norte | 65.52.62.167/32, 40.80.188.24/29 |
| EUA Centro-Sul | 104.210.219.252/32, 13.73.248.112/29 |
| EUA Oeste 2 | 52.183.27.204/32, 20.42.131.224/29 |
| EUA Centro-Oeste | 52.161.25.233/32, 52.150.139.104/29 |
| EUA Oeste | 40.112.150.67/32, 40.82.253.192/29 |
| Canadá Central | 52.228.42.41/32, 52.228.81.248/29 |
| Leste do Canadá | 52.235.36.119/32, 40.89.17.232/29 |
| Sul do Brasil | 191.237.253.115/32, 191.235.225.216/29 |
| Europa do Norte | 40.113.94.67/32, 20.38.85.152/29 |
| Europa Ocidental | 104.40.191.8/32, 20.50.1.0/29 |
| França Central | 52.143.166.54/32, 20.43.42.8/29 |
| Sul de França | 52.136.131.99/32, 51.105.88.248/29 |
| Reino Unido Sul | 51.140.67.72/32, 51.104.25.224/29 |
| Reino Unido Oeste | 51.140.202.34/32, 51.137.161.240/29 |
| Suíça Norte | 51.107.48.224/29 |
| Suíça Oeste | 51.107.144.216/29 |
| Noroeste da Noruega | 51.120.224.216/29 |
| Noruega Leste | 51.120.40.224/29 |
| Ásia Oriental | 23.102.225.54/32, 20.189.108.56/29 |
| Sudeste Asiático | 13.76.81.46/32, 20.43.131.40/29 |
| Austrália Central | 20.37.224.216/29 |
| Austrália Central 2 | 20.36.120.216/29 |
| Leste da Austrália | 13.75.153.240/32, 20.37.195.96/29 |
| Sudeste da Austrália | 13.70.176.196/32, 20.42.227.128/29 |
| Sul da Índia | 104.211.231.18/32, 20.41.193.160/29 |
| Oeste da Índia | 52.136.48.216/29 |
| Leste do Japão | 104.41.161.113/32, 20.43.66.0/29 |
| Oeste do Japão | 23.100.106.151/32, 40.80.57.192/29 |
| Coreia do Sul Central | 52.231.67.75/32, 20.41.65.184/29 |
| Coreia do Sul | 52.231.159.38/32, 40.80.169.176/29 |
| US DoD Leste | 20.140.72.152/29 |
| Gov (US) - Arizona | 20.140.64.152/29 |
| Gov (US) - Arizona | 52.244.75.224/32, 52.244.79.140/32 |
| US Gov - Iowa | 52.244.79.140/32, 52.244.75.224/32 |
| Gov (US) - Texas | 52.238.166.107/32, 52.238.79.29/32 |
| Gov (US) - Virginia | 13.72.17.152/32, 52.227.153.92/32 |
| África do Sul Norte | 102.133.175.72/32 |
| África do Sul Ocidental | 102.133.75.173/32, 102.133.56.128/29, 20.140.48.216/29 |
| Central dos Emirados Emirados Unidos | 20.45.71.151/32, 20.37.64.216/29, 20.140.48.216/29 |
| Emirados Unidos norte | 40.123.216.130/32, 20.38.136.224/29, 20.140.56.136/29 |

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

## <a name="next-steps"></a>Passos Seguintes
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
