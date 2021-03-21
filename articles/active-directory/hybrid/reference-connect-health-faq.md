---
title: Azure Ative Directory Connect Health FAQ - Azure | Microsoft Docs
description: Esta FAQ responde a perguntas sobre Azure AD Connect Health. Estas perguntas mais frequentes incluem perguntas sobre como utilizar o serviço, incluindo o modelo de faturação, capacidades, limitações e suporte.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: f1b851aa-54d7-4cb4-8f5c-60680e2ce866
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6185c5408f74b914ce5ad47634999786ba1d7ab6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368001"
---
# <a name="azure-ad-connect-health-frequently-asked-questions"></a>Azure AD Connect Health frequentemente fez perguntas
Este artigo inclui respostas a perguntas frequentes (FAQs) sobre O Diretório Ativo Azure (Azure AD) Connect Health. Estas FAQs cobrem questões sobre como usar o serviço, que inclui o modelo de faturação, capacidades, limitações e suporte.

## <a name="general-questions"></a>Perguntas gerais
**P: Dirijo vários diretórios de Ad Azure. Como posso mudar para aquele que tem Azure Ative Directory Premium?**

Para alternar entre diferentes inquilinos AD Azure, selecione o nome de **utilizador** atualmente assinado no canto superior direito e, em seguida, escolha a conta apropriada. Se a conta não estiver listada aqui, **selecione Iniciar sê-lo** e, em seguida, utilizar as credenciais de administração global do diretório que tem Azure Ative Directory Premium (P1 ou P2) habilitado a iniciar sação.

**P: Que versão dos papéis de identidade são suportados pela Azure AD Connect Health?**

A tabela que se segue lista as funções e as versões suportadas do sistema operativo.

|Função| Sistema operativo / Versão|
|--|--|
|Serviços de Federação do Active Directory (AD FS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|
|Azure AD Connect | Versão 1.0.9125 ou superior|
|Serviços de Domínio do Active Directory (AD DS)| <ul><li> Windows Server 2012  </li> <li>Windows Server 2012 R2 </li> <li> Windows Server 2016  </li> <li> Windows Server 2019  </li> </ul>|

As instalações do Windows Server Core não são suportadas.

Note que as funcionalidades fornecidas pelo serviço podem diferir com base na função e no sistema operativo. Por outras palavras, todas as funcionalidades podem não estar disponíveis para todas as versões do sistema operativo. Consulte as descrições do recurso para mais detalhes.

**P: Quantas licenças preciso para monitorizar a minha infraestrutura?**

* O primeiro Agente de Saúde Connect requer pelo menos uma licença Azure AD Premium (P1 ou P2).
* Cada agente registado adicional requer 25 licenças adicionais de Azure AD Premium (P1 ou P2).
* A contagem de agentes é equivalente ao número total de agentes registados em todas as funções monitorizadas (AD FS, Azure AD Connect e/ou DS AD).
* O licenciamento AAD Connect Health não requer que atribua a licença a utilizadores específicos. Só precisa de ter o número necessário de licenças válidas.

As informações de licenciamento também são encontradas na página de preços da [AD Azure](https://aka.ms/aadpricing).

Exemplo:

| Agentes registados | Licenças necessárias | Configuração de monitorização de exemplos |
| ------ | --------------- | --- |
| 1 | 1 | 1 servidor Azure Ad Connect |
| 2 | 26| 1 Servidor AD AD Azure e controlador de domínio 1 |
| 3 | 51 | 1 Servidor ativo da Federação de Diretórios (AD FS), 1 proxy AD FS e 1 controlador de domínio |
| 4 | 76 | 1 servidor AD FS, 1 procuração de FS AD e 2 controladores de domínio |
| 5 | 101 | 1 Servidor AD Connect AZure, 1 servidor AD FS, 1 procuração AD FS e 2 controladores de domínio |

**P: A azure AD Connect Health suporta Azure Germany Cloud?**

O Azure AD Connect Health não é suportado na Cloud da Alemanha, exceto na [funcionalidade de relatório de erros de sincronização](how-to-connect-health-sync.md#object-level-synchronization-error-report).

| Funções | Funcionalidades | Apoiado na Nuvem Alemã |
| ------ | --------------- | --- |
| Conecte saúde para sincronização | Monitorização / Insight / Alertas / Análise | No |
|  | Relatório de erro sincronizado | Yes |
| Ligar saúde para ADFS | Monitorização / Insight / Alertas / Análise | No |
| Conecte saúde para ADDS | Monitorização / Insight / Alertas / Análise | No |

Para garantir a conectividade do agente da Connect Health para sincronização, por favor, configure o [requisito de instalação](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) em conformidade.

## <a name="installation-questions"></a>Questões de instalação

**P: Qual é o impacto da instalação do Agente de Saúde Azure AD Connect em servidores individuais?**

O impacto da instalação do Microsoft Azure AD Connect Health Agent, AD FS, servidores de procuração de aplicações web, servidores AD Connect (sincronização) Azure, controladores de domínio é mínimo no que diz respeito ao CPU, consumo de memória, largura de banda de rede e armazenamento.

Os seguintes números são uma aproximação:

* Consumo de CPU: ~1-5% aumento.
* Consumo de memória: Até 10 % da memória total do sistema.

> [!NOTE]
> Se o agente não conseguir comunicar com o Azure, o agente armazena os dados localmente para um limite máximo definido. O agente substitui os dados "em cache" numa base "menos recentemente reparado".
>
>

* Armazenamento de tampão local para Azure AD Connect Health Agents: ~20 MB.
* Para os servidores AD FS, recomendamos que forneça um espaço de disco de 1.024 MB (1 GB) para o canal de auditoria AD FS para os Agentes de Saúde Azure AD Connect Health para processar todos os dados de auditoria antes de serem substituídos.

**P: Terei de reiniciar os meus servidores durante a instalação dos Agentes de Saúde AZure AD Connect?**

N.º A instalação dos agentes não exigirá que reinicie o servidor. No entanto, a instalação de alguns passos pré-requisitos pode exigir um reinício do servidor.

Por exemplo, no Windows Server 2008 R2, a instalação do Quadro .NET 4.5 requer um reboot do servidor.

**P: A Azure AD Connect Health funciona através de um representante http-through?**

Sim. Para as operações em curso, pode configurar o Agente de Saúde para utilizar um representante HTTP para encaminhar pedidos HTTP de saída.
Leia mais sobre [a configuração http Proxy para agentes de saúde](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy).

Se precisar de configurar um representante durante o registo do agente, poderá ter de modificar previamente as definições do seu Proxy do Internet Explorer.

1. Abrir > de **configurações** do Internet  >  **Explorer Opções** de Internet  >    >  **Configurações LAN**.
2. Selecione **Utilize um servidor proxy para o seu LAN**.
3. Selecione **Advanced** se tiver diferentes portas de procuração para HTTP e HTTPS/Secure.

**P: A Azure AD Connect Health suporta a autenticação básica ao ligar-se a proxies HTTP?**

N.º Um mecanismo para especificar um nome de utilizador arbitrário e uma palavra-passe para a autenticação básica não é suportado atualmente.

**P: Que portas de firewall preciso de abrir para que o Agente de Saúde AD Connect AZure funcione?**

Consulte a [secção de requisitos](how-to-connect-health-agent-install.md#requirements) para a lista de portas de firewall e outros requisitos de conectividade.

**P: Por que vejo dois servidores com o mesmo nome no portal Azure AD Connect Health?**

Quando remove um agente de um servidor, o servidor não é removido automaticamente do portal Azure AD Connect Health. Se remover manualmente um agente de um servidor ou remover o próprio servidor, tem de eliminar manualmente a entrada do servidor do portal Azure AD Connect Health.

Pode voltar a imagemr um servidor ou criar um novo servidor com os mesmos detalhes (como o nome da máquina). Se não removeu o servidor já registado do portal Azure AD Connect Health e instalou o agente no novo servidor, poderá ver duas entradas com o mesmo nome.

Neste caso, elimine manualmente a entrada que pertence ao servidor mais antigo. Os dados deste servidor devem estar desatualizados.

**P: Posso instalar o agente de saúde Azure AD Connect no Windows Server Core?**

N.º  A instalação no Núcleo do Servidor não é suportada.

## <a name="health-agent-registration-and-data-freshness"></a>Registo de agentes de saúde e frescura de dados

**P: Quais são as razões comuns para as falhas de registo do Agente de Saúde e como resolvo problemas?**

O agente de saúde pode não se registar devido às seguintes razões possíveis:

* O agente não pode comunicar com os pontos finais necessários porque uma firewall está a bloquear o tráfego. Isto é particularmente comum em servidores proxy de aplicações web. Certifique-se de que permitiu a comunicação de saída aos pontos finais e portas necessários. Consulte a [secção de requisitos](how-to-connect-health-agent-install.md#requirements) para mais detalhes.
* A comunicação de saída é submetida a uma inspeção TLS pela camada de rede. Isto faz com que o certificado que o agente usa seja substituído pelo servidor/entidade de inspeção, e as etapas para completar o registo do agente falham.
* O utilizador não tem acesso à realização do registo do agente. Os administradores globais têm acesso por defeito. Pode utilizar [o controlo de acesso baseado em funções (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac) para delegar o acesso a outros utilizadores.

**P: Estou a ser alertado de que "os dados do Serviço de Saúde não estão atualizados." Como é que resolvo o problema?**

O Azure AD Connect Health gera o alerta quando não recebe todos os pontos de dados do servidor nas últimas duas horas. [Leia mais.](how-to-connect-health-data-freshness.md)

## <a name="operations-questions"></a>Questões de operações
**P: Preciso de permitir a auditoria nos servidores de procuração de aplicações web?**

Não, a auditoria não precisa de ser ativada nos servidores proxy de aplicações web.

**P: Como é que o Azure AD Connect Health Alerts é resolvido?**

Os alertas Azure AD Connect Health são resolvidos com uma condição de sucesso. Azure AD Connect Health Agents deteta e reporta periodicamente as condições de sucesso do serviço. Para alguns alertas, a supressão é baseada no tempo. Por outras palavras, se a mesma condição de erro não for observada no prazo de 72 horas após a geração de alerta, o alerta é automaticamente resolvido.

**P: Estou a ser alertado de que "Pedido de Autenticação de Teste (Transação Sintética) não obteve um token." Como é que resolvo o problema?**

O Azure AD Connect Health para AD FS gera este alerta quando o Agente de Saúde instalado num servidor AD FS não consegue obter um símbolo como parte de uma transação sintética iniciada pelo Agente de Saúde. O agente de saúde usa o contexto do sistema local e tenta obter um símbolo para uma festa de auto-confiação. Trata-se de um teste de captura para garantir que a AD FS se encontre num estado de emissão de fichas.

Na maioria das vezes, este teste falha porque o Agente de Saúde não consegue resolver o nome da fazenda AD FS. Isto pode acontecer se os servidores AD FS estiverem por trás de um balanceador de carga de rede e o pedido for iniciado a partir de um nó que está por trás do equilibrador de carga (ao contrário de um cliente regular que está na frente do equilibrador de carga). Isto pode ser corrigido atualizando o ficheiro "anfitriões" localizado em "C:\Windows\System32\drivers\etc" para incluir o endereço IP do servidor AD FS ou um endereço IP de retorno (127.0.0.1) para o nome da fazenda AD FS (como sts.contoso.com). A adição do ficheiro do anfitrião irá fazer um curto-circuito na chamada de rede, permitindo assim que o Agente de Saúde obtenha o token.

**P: Recebi um e-mail indicando que as minhas máquinas NÃO estão corrigidas para os recentes ataques de ransomware. Por que recebi este e-mail?**

O serviço Azure AD Connect Health analisou todas as máquinas que monitoriza para garantir a instalação dos patches necessários. O e-mail foi enviado aos administradores do arrendatário se pelo menos uma máquina não tivesse os patches críticos. A lógica que se segue foi usada para fazer esta determinação.
1. Encontre todos os hotfixs instalados na máquina.
2. Verifique se pelo menos um dos HotFixes da lista definida está presente.
3. Se sim, a máquina está protegida. Se não, a máquina está em risco para o ataque.

Pode utilizar o seguinte script PowerShell para realizar esta verificação manualmente. Implementa a lógica acima.

```powershell
Function CheckForMS17-010 ()
{
    $hotfixes = "KB3205409", "KB3210720", "KB3210721", "KB3212646", "KB3213986", "KB4012212", "KB4012213", "KB4012214", "KB4012215", "KB4012216", "KB4012217", "KB4012218", "KB4012220", "KB4012598", "KB4012606", "KB4013198", "KB4013389", "KB4013429", "KB4015217", "KB4015438", "KB4015546", "KB4015547", "KB4015548", "KB4015549", "KB4015550", "KB4015551", "KB4015552", "KB4015553", "KB4015554", "KB4016635", "KB4019213", "KB4019214", "KB4019215", "KB4019216", "KB4019263", "KB4019264", "KB4019472", "KB4015221", "KB4019474", "KB4015219", "KB4019473"

    #checks the computer it's run on if any of the listed hotfixes are present
    $hotfix = Get-HotFix -ComputerName $env:computername | Where-Object {$hotfixes -contains $_.HotfixID} | Select-Object -property "HotFixID"

    #confirms whether hotfix is found or not
    if (Get-HotFix | Where-Object {$hotfixes -contains $_.HotfixID})
    {
        "Found HotFix: " + $hotfix.HotFixID
    } else {
        "Didn't Find HotFix"
    }
}

CheckForMS17-010

```

**P: Porque é que o cmdlet PowerShell <i>Get-MsolDirSyncProvisioningError</i> mostra menos erros de sincronização no resultado?**

<i>A Get-MsolDirSyncProvisioningError</i> só devolverá erros de provisões dirSync. Além disso, o portal Connect Health também mostra outros tipos de erros de sincronização, como erros de exportação. Isto é consistente com o resultado delta Azure AD Connect. Leia mais sobre [erros do Azure AD Connect Sync](./tshoot-connect-sync-errors.md).

**P: Porque é que as minhas auditorias da ADFS não estão a ser geradas?**

Utilize o PowerShell cmdlet <i>Get-AdfsProperties -AuditLevel</i> para garantir que os registos de auditoria não estão em estado de desativado. Leia mais sobre [os registos de auditoria da ADFS.](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server#auditing-levels-in-ad-fs-for-windows-server-2016) Note que se houver definições de auditoria avançadas empurradas para o servidor ADFS, quaisquer alterações com auditpol.exe serão substituídas (evento se a Aplicação Gerada não estiver configurada). Neste caso, por favor, desa esta medida, desa um por favor, desateia a política de segurança local para registar falhas geradas pela aplicação e sucesso.

**P: Quando é que o certificado de agente será renovado automaticamente antes do termo?**
A certificação do agente será renovada automaticamente **6 meses** antes da data de validade. Se não for renovado, certifique-se de que a ligação de rede do agente está estável. Reiniciar os serviços do agente ou atualizar para a versão mais recente também pode resolver o problema.



## <a name="related-links"></a>Ligações relacionadas
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalação do Agente de Saúde Azure AD Connect](how-to-connect-health-agent-install.md)
* [Azure AD Connect Operações de Saúde](how-to-connect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Usando Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md)
* [Histórico das versões do Azure AD Connect Health](reference-connect-health-version-history.md)
