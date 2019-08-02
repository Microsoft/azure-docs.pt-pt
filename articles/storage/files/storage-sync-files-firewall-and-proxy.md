---
title: Sincronização de Arquivos do Azure configurações de proxy e firewall local | Microsoft Docs
description: Configuração de rede local Sincronização de Arquivos do Azure
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 69f4c767b9fc1da90db021ffb3eb8704983ca69b
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699312"
---
# <a name="azure-file-sync-proxy-and-firewall-settings"></a>As definições do proxy e da firewall do Azure File Sync
Sincronização de Arquivos do Azure conecta seus servidores locais a arquivos do Azure, habilitando a sincronização de vários sites e recursos de camadas de nuvem. Como tal, um servidor local deve estar conectado à Internet. Um administrador de ti precisa decidir o melhor caminho para o servidor acessar os serviços de nuvem do Azure.

Este artigo fornecerá informações sobre requisitos específicos e opções disponíveis para conectar com êxito e segurança seu servidor ao Sincronização de Arquivos do Azure.

## <a name="overview"></a>Descrição geral
Sincronização de Arquivos do Azure atua como um serviço de orquestração entre o Windows Server, o compartilhamento de arquivos do Azure e vários outros serviços do Azure para sincronizar dados, conforme descrito em seu grupo de sincronização. Para que Sincronização de Arquivos do Azure funcionem corretamente, você precisará configurar seus servidores para se comunicar com os seguintes serviços do Azure:

- Storage do Azure
- Azure File Sync
- Azure Resource Manager
- Serviços de autenticação

> [!Note]  
> O agente de Sincronização de Arquivos do Azure no Windows Server inicia todas as solicitações para serviços de nuvem, o que resulta em apenas considerar o tráfego de saída de uma perspectiva de firewall. <br /> Nenhum serviço do Azure inicia uma conexão com o agente de Sincronização de Arquivos do Azure.

## <a name="ports"></a>Portas
Sincronização de Arquivos do Azure move dados de arquivo e metadados exclusivamente por HTTPS e requer que a porta 443 seja aberta de saída.
Como resultado, todo o tráfego é criptografado.

## <a name="networks-and-special-connections-to-azure"></a>Redes e conexões especiais com o Azure
O agente de Sincronização de Arquivos do Azure não tem requisitos relacionados a canais especiais, como o [ExpressRoute](../../expressroute/expressroute-introduction.md), etc. para o Azure.

Sincronização de Arquivos do Azure funcionará por meio de qualquer meio disponível que permita o alcance do Azure, adaptando-se automaticamente a várias características de rede, como largura de banda, latência, além de oferecer controle de administrador para ajuste fino. Nem todos os recursos estão disponíveis no momento. Se você quiser configurar um comportamento específico, informe-nos por meio [do Azure files UserVoice](https://feedback.azure.com/forums/217298-storage?category_id=180670).

## <a name="proxy"></a>Proxy
O Sincronização de Arquivos do Azure dá suporte a configurações de proxy de todo o computador e do aplicativo.

**As configurações de proxy específicas do aplicativo** permitem a configuração de um proxy especificamente para o tráfego de sincronização de arquivos do Azure. As configurações de proxy específicas do aplicativo têm suporte na versão do agente 4.0.1.0 ou mais recente e podem ser configuradas durante a instalação do agente ou usando o cmdlet do PowerShell Set-StorageSyncProxyConfiguration.

Comandos do PowerShell para definir configurações de proxy específicas do aplicativo:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Set-StorageSyncProxyConfiguration -Address <url> -Port <port number> -ProxyCredential <credentials>
```
**As configurações de proxy de todo o computador** são transparentes para o agente de sincronização de arquivos do Azure, pois todo o tráfego do servidor é roteado pelo proxy.

Para definir as configurações de proxy de todo o computador, siga as etapas abaixo: 

1. Definir configurações de proxy para aplicativos .NET 

   - Edite estes dois arquivos:  
     C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config  
     C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config

   - Adicione a seção < System. net > nos arquivos Machine. config (abaixo da seção < System. serviceModel >).  Altere 127.0.01:8888 para o endereço IP e a porta do servidor proxy. 
     ```
      <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
          <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
      </system.net>
     ```

2. Definir as configurações de proxy do WinHTTP 

   - Execute o seguinte comando em um prompt de comando elevado ou no PowerShell para ver a configuração de proxy existente:   

     netsh WinHTTP show proxy

   - Execute o comando a seguir em um prompt de comando elevado ou no PowerShell para definir a configuração de proxy (alterar 127.0.01:8888 para o endereço IP e a porta do servidor proxy):  

     netsh WinHTTP Set proxy 127.0.0.1:8888

3. Reinicie o serviço do agente de sincronização de armazenamento executando o seguinte comando em um prompt de comando elevado ou PowerShell: 

      filesyncsvc net stop

      Nota: O serviço filesyncsvc (agente de sincronização de armazenamento) será iniciado automaticamente quando for interrompido.

## <a name="firewall"></a>Firewall
Como mencionado em uma seção anterior, a porta 443 precisa ser aberta de saída. Com base em políticas em seu datacenter, ramificação ou região, restringir ainda mais o tráfego nessa porta para domínios específicos pode ser desejado ou necessário.

A tabela a seguir descreve os domínios necessários para a comunicação:

| Serviço | Ponto de extremidade de nuvem pública | Ponto de extremidade do Azure governamental | Utilização |
|---------|----------------|---------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | https://management.usgovcloudapi.net | Qualquer chamada de usuário (como o PowerShell) vai para/por essa URL, incluindo a chamada de registro de servidor inicial. |
| **Azure Active Directory** | https://login.windows.net | https://login.microsoftonline.us | Azure Resource Manager chamadas devem ser feitas por um usuário autenticado. Para ter sucesso, essa URL é usada para autenticação de usuário. |
| **Azure Active Directory** | https://graph.windows.net/ | https://graph.windows.net/ | Como parte da implantação de Sincronização de Arquivos do Azure, uma entidade de serviço no Azure Active Directory da assinatura será criada. Essa URL é usada para isso. Essa entidade de segurança é usada para delegar um conjunto mínimo de direitos ao serviço de Sincronização de Arquivos do Azure. O usuário que executa a configuração inicial do Sincronização de Arquivos do Azure deve ser um usuário autenticado com privilégios de proprietário da assinatura. |
| **Armazenamento do Azure** | &ast;.core.windows.net | &ast;.core.usgovcloudapi.net | Quando o servidor baixa um arquivo, o servidor executa essa movimentação de dados com mais eficiência ao conversar diretamente com o compartilhamento de arquivos do Azure na conta de armazenamento. O servidor tem uma chave SAS que permite apenas o acesso de compartilhamento de arquivos direcionado. |
| **Sincronização de Arquivos do Azure** | &ast;.one.microsoft.com | &ast;.afs.azure.us | Após o registro inicial do servidor, o servidor recebe uma URL regional para a instância do serviço de Sincronização de Arquivos do Azure nessa região. O servidor pode usar a URL para se comunicar de forma direta e eficiente com a instância que manipula sua sincronização. |
| **PKI da Microsoft** | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | `https://www.microsoft.com/pki/mscorp`<br /><http://ocsp.msocsp.com> | Depois que o agente de Sincronização de Arquivos do Azure estiver instalado, a URL PKI será usada para baixar os certificados intermediários necessários para se comunicar com o serviço Sincronização de Arquivos do Azure e o compartilhamento de arquivos do Azure. A URL OCSP é usada para verificar o status de um certificado. |

> [!Important]
> Ao permitir o tráfego &ast;para. One.Microsoft.com, o tráfego para mais do que apenas o serviço de sincronização é possível no servidor. Há muito mais serviços da Microsoft disponíveis em subdomínios.

Se &ast;. One.Microsoft.com for muito amplo, você poderá limitar a comunicação do servidor, permitindo a comunicação apenas com instâncias regionais explícitas do serviço de sincronização de arquivos do Azure. Quais instâncias escolher dependem da região do serviço de sincronização de armazenamento que você implantou e registrou o servidor. Essa região é chamada de "URL do ponto de extremidade primário" na tabela a seguir.

Para fins de BCDR (continuidade dos negócios e recuperação de desastre), você pode ter especificado os compartilhamentos de arquivos do Azure em uma conta de armazenamento GRS (globalmente redundante). Se esse for o caso, os compartilhamentos de arquivos do Azure realizarão failover para a região emparelhada no caso de uma interrupção regional duradoura. Sincronização de Arquivos do Azure usa os mesmos emparelhamentos regionais que o armazenamento. Portanto, se você usar contas de armazenamento GRS, será necessário habilitar URLs adicionais para permitir que o servidor se comunique com a região emparelhada para Sincronização de Arquivos do Azure. A tabela a seguir chama essa "região emparelhada". Além disso, há uma URL de perfil do Gerenciador de tráfego que também precisa ser habilitada. Isso garantirá que o tráfego de rede possa ser redirecionado diretamente para a região emparelhada em caso de failover e seja chamado de "URL de descoberta" na tabela a seguir.

| Nuvem  | Região | URL do ponto de extremidade primário | Região emparelhada | URL de Deteção |
|--------|--------|----------------------|---------------|---------------|
| Público |Leste da Austrália | https:\//kailani-aue.one.microsoft.com | Sudeste da Austrália | https:\//tm-kailani-aue.one.microsoft.com |
| Público |Sudeste da Austrália | https:\//kailani-aus.one.microsoft.com | Leste da Austrália | https:\//tm-kailani-aus.one.microsoft.com |
| Público | Sul do Brasil | https:\//brazilsouth01.AFS.Azure.net | EUA Centro-Sul | https:\//TM-brazilsouth01.AFS.Azure.net |
| Público | Canadá Central | https:\//kailani-cac.one.microsoft.com | Leste do Canadá | https:\//tm-kailani-cac.one.microsoft.com |
| Público | Leste do Canadá | https:\//kailani-cae.one.microsoft.com | Canadá Central | https:\//tm-kailani.cae.one.microsoft.com |
| Público | Índia Central | https:\//kailani-cin.one.microsoft.com | Sul da Índia | https:\//tm-kailani-cin.one.microsoft.com |
| Público | EUA Central | https:\//kailani-cus.one.microsoft.com | EUA Leste 2 | https:\//tm-kailani-cus.one.microsoft.com |
| Público | Ásia Oriental | https:\//kailani11.One.Microsoft.com | Sudeste Asiático | https:\//TM-kailani11.One.Microsoft.com |
| Público | East US | https:\//kailani1.one.microsoft.com | EUA Oeste | https:\//tm-kailani1.one.microsoft.com |
| Público | EUA Leste 2 | https:\//kailani-ess.one.microsoft.com | EUA Central | https:\//tm-kailani-ess.one.microsoft.com |
| Público | Leste do Japão | https:\//japaneast01.AFS.Azure.net | Oeste do Japão | https:\//TM-japaneast01.AFS.Azure.net |
| Público | Oeste do Japão | https:\//japanwest01.afs.azure.net | Leste do Japão | https:\//TM-japanwest01.AFS.Azure.net |
| Público | Coreia do Sul Central | https:\//koreacentral01.AFS.Azure.net/ | Coreia do Sul | https:\//TM-koreacentral01.AFS.Azure.net/ |
| Público | Coreia do Sul | https:\//koreasouth01.AFS.Azure.net/ | Coreia do Sul Central | https:\//TM-koreasouth01.AFS.Azure.net/ |
| Público | EUA Centro-Norte | https:\//northcentralus01.AFS.Azure.net | EUA Centro-Sul | https:\//TM-northcentralus01.AFS.Azure.net |
| Público | Europa do Norte | https:\//kailani7.one.microsoft.com | Europa Ocidental | https:\//tm-kailani7.one.microsoft.com |
| Público | EUA Centro-Sul | https:\//southcentralus01.AFS.Azure.net | EUA Centro-Norte | https:\//TM-southcentralus01.AFS.Azure.net |
| Público | Sul da Índia | https:\//kailani-sin.one.microsoft.com | Índia Central | https:\//tm-kailani-sin.one.microsoft.com |
| Público | Sudeste Asiático | https:\//kailani10.one.microsoft.com | Ásia Oriental | https:\//tm-kailani10.one.microsoft.com |
| Público | Reino Unido Sul | https:\//kailani-uks.one.microsoft.com | Reino Unido Oeste | https:\//tm-kailani-uks.one.microsoft.com |
| Público | Reino Unido Oeste | https:\//kailani-ukw.one.microsoft.com | Reino Unido Sul | https:\//tm-kailani-ukw.one.microsoft.com |
| Público | EUA Centro-Oeste | https:\//westcentralus01.AFS.Azure.net | EUA Oeste 2 | https:\//TM-westcentralus01.AFS.Azure.net |
| Público | Europa Ocidental | https:\//kailani6.one.microsoft.com | Europa do Norte | https:\//tm-kailani6.one.microsoft.com |
| Público | EUA Oeste | https:\//kailani.one.microsoft.com | East US | https:\//tm-kailani.one.microsoft.com |
| Público | EUA Oeste 2 | https:\//westus201.AFS.Azure.net | EUA Centro-Oeste | https:\//TM-westus201.AFS.Azure.net |
| Governo | Gov (US) - Arizona | https:\//usgovarizona01.AFS.Azure.us | Gov (US) - Texas | https:\//TM-usgovarizona01.AFS.Azure.us |
| Governo | Gov (US) - Texas | https:\//usgovtexas01.afs.azure.us | Gov (US) - Arizona | https:\//tm-usgovtexas01.afs.azure.us |

- Se você usar contas de armazenamento com redundância local (LRS) ou com redundância de zona (ZRS), você só precisará habilitar a URL listada em "URL do ponto de extremidade primário".

- Se você usar contas de armazenamento GRS (com redundância global), habilite três URLs.

**Example:** Você implanta um serviço de sincronização de `"West US"` armazenamento no e registra seu servidor com ele. As URLs para permitir que o servidor se comunique para esse caso são:

> - https:\//Kailani.One.Microsoft.com (ponto de extremidade primário: Oeste dos EUA)
> - https:\//kailani1.One.Microsoft.com (região de failover emparelhada: Leste dos EUA)
> - https:\//TM-Kailani.One.Microsoft.com (URL de descoberta da região primária)

## <a name="summary-and-risk-limitation"></a>Resumo e limitação de risco
As listas anteriores neste documento contêm as URLs às quais Sincronização de Arquivos do Azure se comunica atualmente. Os firewalls devem ser capazes de permitir o tráfego de saída para esses domínios. A Microsoft se esforça para manter essa lista atualizada.

A configuração de regras de firewall de restrição de domínio pode ser uma medida para melhorar a segurança. Se essas configurações de firewall forem usadas, será necessário ter em mente que as URLs serão adicionadas e podem até mesmo mudar ao longo do tempo. Verifique este artigo periodicamente.

## <a name="next-steps"></a>Passos seguintes
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
