---
title: Linha de segurança Azure para Azure Stack Edge
description: A linha de base de segurança Azure Stack Edge fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Benchmark de Segurança Azure.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 460fd66ed4651248639334caa55eb8facbce866d
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452378"
---
# <a name="azure-security-baseline-for-azure-stack-edge"></a>Linha de segurança Azure para Azure Stack Edge

Esta linha de base de segurança aplica orientações da [versão 2.0 do Azure Security Benchmark](../security/benchmarks/overview.md) para o Microsoft Azure Stack Edge. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure. O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável ao Azure Stack Edge. Foram excluídos **os controlos** não aplicáveis ao Azure Stack Edge.

Para ver como o Azure Stack Edge mapeia completamente para o Benchmark de Segurança Azure, consulte o [ficheiro de mapeamento de base de base de segurança Azure Stack Edge](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Segurança de Rede

*Para obter mais informações, veja [Referência de Segurança do Azure: Segurança de Rede](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: Implementar a segurança para o tráfego interno

**Orientação**: Os clientes implementam um dispositivo Azure Stack Edge fornecido pela Microsoft na sua rede privada para acesso interno e têm opções para o proteger ainda mais. Por exemplo, o dispositivo Azure Stack Edge está acessível sobre a rede interna do cliente e requer um IP configurado pelo cliente. Além disso, uma senha de acesso é escolhida pelo cliente para aceder à interface de utilizador do dispositivo. 

O tráfego interno é ainda assegurado por:

- A versão 1.2 de Segurança da Camada de Transporte (TLS) é necessária para o portal Azure e para a gestão SDK do dispositivo Azure Stack Edge.

- Qualquer acesso do cliente ao dispositivo é através da UI web local utilizando o TLS 1.2 padrão como protocolo de segurança predefinido.

- Apenas um dispositivo autorizado Azure Stack Edge Pro é autorizado a aderir ao serviço Azure Stack Edge que o cliente criou na sua subscrição Azure.

Informações adicionais estão disponíveis nos links referenciados.
 
- [Configure TLS 1.2 em clientes windows que acedem ao dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-j-series-configure-tls-settings.md)

- [Quickstart - Começa com o Azure Stack Edge Pro com GPU](azure-stack-edge-gpu-quickstart.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Ligar redes privadas em conjunto

**Orientação**: Os clientes podem escolher uma rede privada virtual ponto-a-local (VPN) para ligar um dispositivo Azure Stack Edge da sua rede privada no local à rede Azure. A VPN fornece uma segunda camada de encriptação para a segurança da camada de transporte sobre os dados em movimento do dispositivo do cliente para o Azure. 

Os clientes podem configurar uma rede privada virtual no seu dispositivo Azure Stack Edge através do portal Azure ou através do Azure PowerShell.

- [Configure Azure VPN via Azure PowerShell script para Azure Stack Edge Pro R e Azure Stack Edge Mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Configure TLS 1.2 em clientes windows que acedem ao dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-j-series-configure-tls-settings.md)

- [Tutorial: Configure certificados para o seu Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: Estabelecer o acesso à rede privada para os serviços do Azure

**Orientação**: Os clientes podem escolher uma rede privada virtual ponto-a-local (VPN) para ligar um dispositivo Azure Stack Edge da sua rede privada no local à rede Azure. A VPN fornece uma segunda camada de encriptação para a segurança da camada de transporte sobre os dados em movimento do dispositivo do cliente para o Azure. 

- [Configure Azure VPN via Azure PowerShell script para Azure Stack Edge Pro R e Azure Stack Edge Mini R](azure-stack-edge-mini-r-configure-vpn-powershell.md)

- [Configure TLS 1.2 em clientes windows que acedem ao dispositivo GPU Azure Stack Edge Pro](azure-stack-edge-j-series-configure-tls-settings.md)

- [Tutorial: Configure certificados para o seu Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: Proteger aplicações e serviços contra ataques de rede externa

**Orientação**: O dispositivo Azure Stack Edge incorpora funcionalidades padrão de proteção da rede do Windows Server, que não são configuráveis pelos clientes.

Os clientes podem optar por proteger a sua rede privada ligada ao dispositivo Azure Stack Edge de ataques externos utilizando um aparelho virtual de rede, como uma firewall com proteções avançadas de negação de serviço distribuídas (DDoS).

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: Implementar sistemas de deteção/prevenção de intrusões (IDS/IPS)

**Orientação**: Os pontos finais utilizados pelo dispositivo Azure Stack Edge são todos geridos pela Microsoft. Os clientes são responsáveis por quaisquer controlos adicionais que desejem implementar nos seus sistemas no local.

O dispositivo Azure Stack Edge utiliza as suas próprias funcionalidades de deteção de intrusões para proteger o serviço. 

- [Compreenda a segurança do Azure Stack Edge](azure-stack-edge-security.md)

- [Informações portuárias para Azure Stack Edge](azure-stack-edge-gpu-system-requirements.md)

- [Obtenha registos de deteção de hardware e software](azure-stack-edge-gpu-troubleshoot.md#gather-advanced-security-logs)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="identity-management"></a>Gestão de Identidades

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Gerir identidades de aplicação de forma segura e automática

**Orientação**: Todos os dispositivos Azure Stack Edge têm automaticamente uma identidade gerida atribuída ao sistema no Azure Ative Directory (Azure AD). Atualmente, a identidade gerida é usada para a gestão em nuvem de máquinas virtuais hospedadas no Azure Stack Edge.

Os dispositivos Azure Stack Edge iniciam-se num estado bloqueado para acesso local. Para a conta de administrador de dispositivo local, terá de se ligar ao seu dispositivo através da interface de utilizador web local ou da interface PowerShell e definir uma palavra-passe forte. Guarde e gere a credencial do administrador do dispositivo num local seguro, como um Cofre de Chave Azure e rode a palavra-passe de administração de acordo com os seus padrões organizacionais.

- [Proteja o dispositivo Azure Stack Edge através de senha](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Utilizar o início de sessão único (SSO) do Azure AD para acesso a aplicações

**Orientação**: O sinal único não é suportado para dispositivos de ponta de ponta Azure Stack Edge. No entanto, pode optar por ativar o diretório padrão Azure Ative (Azure AD) com base num único sinal para garantir o acesso aos seus recursos em nuvem Azure.

- [Compreender a aplicação SSO com Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Utilizar controlos de autenticação fortes para todos os acessos baseados no Azure Active Directory

**Orientação**: A autenticação multifactor é um forte controlo de autenticação, mas uma funcionalidade de opt-in para os utilizadores do serviço Azure Stack Edge. Pode ser alavancado para cenários suportados, como a gestão de bordas de dispositivos Azure Stack Edge no portal Azure. Note que o acesso local aos dispositivos Azure Stack Edge não suporta a autenticação multifactor.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Monitorizar e alertar para anomalias em contas

**Orientação**: Ative o Azure Monitor a recolher registos de dispositivos ou contentores para o seu serviço Azure Stack Edge. Monitorize recipientes, como os que executam múltiplas aplicações de computação no cluster Kubernetes no seu dispositivo.

Além disso, um pacote de suporte, que inclui registos de auditoria, pode ser recolhido na interface de utilizador web local do dispositivo Azure Stack Edge. Note que o pacote de suporte não está disponível no portal Azure.
 
- [Ativar o Monitor Azure no seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-enable-azure-monitor.md) 

- [Reúna um pacote de apoio](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restringir o acesso aos recursos do Azure com base em condições

**Orientação**: O acesso condicional do Azure Ative Directory (Azure AD) é uma funcionalidade de opt-in para autenticação ao serviço Azure Stack Edge. O serviço Azure Stack Edge é um recurso no portal Azure que permite gerir um dispositivo Azure Stack Edge Pro em diferentes localizações geográficas. 

- [O que é acesso condicional](../active-directory/conditional-access/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: Eliminar a exposição de credenciais involuntária

**Orientação**: Siga as melhores práticas para proteger as credenciais, tais como:

- Chave de ativação utilizada para ativar o dispositivo com o recurso Azure Stack Edge em Azure.
- Inscreva-se em credenciais para aceder ao dispositivo Azure Stack Edge.
- Ficheiros chave que podem facilitar a recuperação do dispositivo Azure Stack Edge.
- Chave de encriptação do canal

Rode e, em seguida, sincronize regularmente as chaves da sua conta de armazenamento para ajudar a proteger a sua conta de armazenamento de utilizadores não autorizados.

- [Segurança e proteção de dados Azure Stack Edge Pro](azure-stack-edge-security.md)

- [Sincronizar chaves de armazenamento](azure-stack-edge-manage-shares.md#sync-storage-keys)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="privileged-access"></a>Acesso Privilegiado

*Para obter mais informações, veja [Referência de Segurança do Azure: Acesso Privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restringir o acesso administrativo a sistemas críticos da empresa

**Orientação**: A solução Azure Stack Edge tem vários componentes com fortes controlos de acesso para restringir o acesso a dispositivos críticos do negócio. A sua organização necessitará de um Enterprise Agreement (EA) ou cloud Solution Provider (CSP) ou de uma subscrição de Patrocínio do Microsoft Azure para configurar e gerir o dispositivo: 

O serviço Azure Stack Edge está protegido pelas funcionalidades de segurança Azure como um serviço de gestão hospedado no Azure. Pode obter a chave de encriptação para o seu recurso nas propriedades do Dispositivo para quaisquer operações de gestão de kits de desenvolvimento de software, mas só pode ver a chave de encriptação se tiver as permissões adequadas para a API do Gráfico de Recurso.

O dispositivo Azure Stack Edge Pro é um dispositivo no local que ajuda a transformar os seus dados processando-os localmente e enviando-os depois para o Azure. O seu dispositivo:

- precisa de uma chave de ativação para aceder ao serviço Azure Stack Edge.
- está sempre protegido por uma palavra-passe do dispositivo.
- tem arranque seguro ativado.

- é um dispositivo bloqueado. O controlador de gestão de quadros base do dispositivo (BMC) e o BIOS estão protegidos por palavra-passe. O BIOS está protegido por acesso limitado ao utilizador.
- executa o Windows Defender Device Guard. O Device Guard permite-lhe executar apenas aplicações fidedignas que define nas suas políticas de integridade de código.

Siga as melhores práticas para proteger todas as credenciais e segredos usados para aceder ao Azure Stack Edge. 

- [Melhores práticas de palavra-passe](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Rever e reconciliar o acesso dos utilizadores regularmente

**Orientação**: Azure Stack Edge tem um utilizador chamado 'EdgeUser' que pode configurar o dispositivo. Possui ainda o utilizador 'EdgeArmUser' do Azure Resource Manager para as funcionalidades do Azure Resource Manager no dispositivo. 

Devem ser seguidas as melhores práticas para proteger:

- Credenciais utilizadas para aceder ao dispositivo no local

- SMB partilha credenciais.

- Acesso a sistemas de clientes configurados para utilizar ações da NFS.

- Chaves de conta de armazenamento local utilizadas para aceder às contas de armazenamento locais quando utilizam a API Blob REST.

Informações adicionais estão disponíveis no link referenciado.

- [Informações sobre segurança para os seus dispositivos Azure Stack Edge](azure-stack-edge-security.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Utilizar estações de trabalho privilegiadas

**Orientação**: Postos de trabalho seguros e isolados são de importância crucial para a segurança de funções sensíveis como administradores, desenvolvedores e operadores de serviços críticos. Utilize estações de trabalho de utilizador altamente seguras com ou sem Bastião Azure para tarefas administrativas. Utilize o Azure Ative Directory (Azure AD), o Microsoft Defender Advanced Threat Protection (ATP) e o Microsoft Intune para implementar uma estação de trabalho segura e gerida para tarefas administrativas. 

As estações de trabalho seguras podem ser geridas centralmente para impor uma configuração segura, incluindo uma autenticação forte e linhas de base de software e hardware e acesso de rede e lógico restrito.

- [Compreender estações de trabalho de acesso privilegiada](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Implementar uma estação de trabalho de acesso privilegiado](/security/compass/privileged-access-deployment)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de Dados

*Para obter mais informações, veja [Referência de Segurança do Azure: Proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Proteger dados confidenciais

**Orientação**: A azure Stack Edge trata todos os dados interacdos como sensíveis, com apenas utilizadores autorizados a terem acesso a estes dados. Deve seguir as melhores práticas para proteger as credenciais utilizadas para aceder ao serviço Azure Stack Edge.

- [Segurança e proteção de dados Azure Stack Edge Pro](azure-stack-edge-security.md#protect-the-device-via-password)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Encriptar informações confidenciais em trânsito

**Orientação**: Azure Stack Edge utiliza canais seguros para dados em voo. Esses avisos são:

- O Padrão TLS 1.2 é utilizado para dados que viajam entre o dispositivo e a nuvem Azure. Não há recuo para tLS 1.1 e mais cedo. A comunicação do agente será bloqueada se o TLS 1.2 não for suportado. TLS 1.2 também é necessário para a gestão do portal Azure e do kit de desenvolvimento de software (SDK).

- Quando os clientes acedem ao seu dispositivo através da interface de utilizador web local de um navegador, o padrão TLS 1.2 é usado como o protocolo de segurança padrão

A melhor prática é configurar o seu navegador para usar o TLS 1.2. Utilize o SMB 3.0 com encriptação para proteger os dados quando os copiar dos seus servidores de dados.

- [Melhores práticas para proteger dados em voo](azure-stack-edge-security.md#protect-data-in-flight)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="asset-management"></a>Gestão de Recursos

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão de Ativos](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Certifique-se de que a equipa de segurança tem visibilidade para os riscos dos ativos

**Orientação**: Confirme que as equipas de segurança recebem as permissões Leitor de Segurança no seu inquilino e nas suas subscrições do Azure, para que possam monitorizar os riscos de segurança com o Centro de Segurança do Azure. 

Dependendo da forma como as responsabilidades destas equipas são estruturadas, a monitorização dos riscos de segurança pode ser da responsabilidade de uma equipa de segurança central ou de uma equipa local. Dito isto, as informações e os riscos de segurança têm de ser sempre agregados centralmente nas organizações. 

As permissões Leitor de Segurança podem ser aplicadas de um modo amplo em todo um inquilino (Grupo de Gestão Raiz) ou dentro de âmbitos, como grupos de gestão ou subscrições específicas. 

Note que podem ser necessárias permissões adicionais para obter visibilidade em cargas de trabalho e serviços. 

- [Descrição Geral da Função de Leitor de Segurança](../role-based-access-control/built-in-roles.md#security-reader)

- [Descrição Geral dos Grupos de Gestão do Azure](../governance/management-groups/overview.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitar a capacidade dos utilizadores de interagirem com o Gestor de Recursos Azure

**Orientação**: Apenas utilizadores autorizados, por exemplo, o 'EdgeArmUser' pode aceder ao dispositivo Azure Stack Edge APIs através do Gestor de Recursos Azure local. As palavras-passe da conta de utilizador só podem ser geridas no portal Azure. 

- [Definir a palavra-passe do Azure Resource Manager](azure-stack-edge-j-series-set-azure-resource-manager-password.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Utilize apenas aplicações aprovadas em recursos computacional

**Orientação**: Pode trazer as suas próprias aplicações para executar em quaisquer máquinas virtuais criadas localmente. Utilize scripts PowerShell para criar máquinas virtuais de computação local no seu dispositivo Stack Edge. Recomendamos vivamente que traga apenas aplicações fidedignas para executar nas máquinas virtuais locais. 

- [Como controlar a execução do script PowerShell em ambiente Windows](/powershell/module/microsoft.powershell.security/set-executionpolicy)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-threat-detection"></a>Registos e Deteção de Ameaças

*Para obter mais informações, veja [Referência de Segurança do Azure: Registos e Deteção de Ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Permitir a deteção de ameaças para recursos Azure

**Orientação**: Azure Stack Edge não oferece capacidades de deteção de ameaças. No entanto, os clientes podem recolher registos de auditoria num pacote de suporte para deteção e análise de ameaças offline. 

- [Recolher pacote de suporte para dispositivo Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Ativar o registo para atividades de rede do Azure

**Orientação**: Azure Stack Edge tem registos de auditoria de rede ativados como parte do pacote de suporte transferível. Estes registos podem ser analisados para implementar uma monitorização semirreal para os seus dispositivos Azure Stack Edge.

- [Azure Stack Edge reúne um pacote de suporte](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Ativar o registo dos recursos do Azure

**Orientação**: A monitorização em tempo real com troncos não é atualmente suportada para Azure Stack Edge. Existe uma capacidade para recolher um pacote de suporte que lhe permite analisar os vários registos incluídos no mesmo, tais como firewall, software, intrusão de hardware e registos de eventos do sistema para o seu dispositivo Azure Stack Edge Pro. Note que a intrusão do software ou os registos de firewall predefinidos são recolhidos para o tráfego de entrada e saída.

- [Recolha um pacote de suporte para Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar a análise e gestão do registo de segurança

**Orientação**: A monitorização em tempo real com troncos não é atualmente suportada para Azure Stack Edge. No entanto, pode recolher um pacote de suporte que lhe permite analisar os vários registos incluídos no mesmo.  O pacote de suporte é comprimido e é descarregado para o caminho à sua escolha. Desaperte o pacote e a visualização dos ficheiros de registo do sistema contidos nele. Também pode enviar estes registos para uma ferramenta de gestão de eventos de informação de segurança ou outro local de armazenamento central para análise.

- [Recolha um pacote de suporte para Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-6-configure-log-storage-retention"></a>LT-6: Configurar a retenção de armazenamento dos registos

**Orientação**: O período de retenção de registo não pode ser alterado no dispositivo Azure Stack Edge. Os troncos mais antigos são purgados conforme necessário. Pode recolher pacotes de suporte do dispositivo em intervalos periódicos para que quaisquer requisitos retenha os registos por um período de tempo mais longo. 

- [Recolha um pacote de suporte para Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: Azure Stack Edge utiliza time.windows.com, um servidor de fornecedor de tempo de rede da Microsoft.  O Azure Stack Edge também permite ao cliente configurar o servidor de protocolo de tempo de rede à sua escolha.

- [Configurar configurações de tempo do dispositivo Azure Stack Edge](azure-stack-edge-gpu-deploy-set-up-device-update-time.md#configure-time)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a Incidentes

*Para obter mais informações, veja [Referência de Segurança do Azure: Resposta a Incidentes](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparação – atualizar o processo de resposta a incidentes para o Azure

**Orientação**: Certifique-se de que o seu plano de resposta a incidentes organizacionais inclui processos: 

- para responder a incidentes de segurança

- que foram atualizados para a nuvem Azure
 
- são regularmente exercidas para garantir a prontidão

Recomenda-se implementar a segurança utilizando processos de resposta a incidentes padronizados em todo o ambiente da empresa.

- [Implementar a segurança em todo o ambiente empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guia de referência da resposta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparação – configurar a notificação de incidentes

**Orientação**: Configure as informações de contacto dos incidentes de segurança no Centro de Segurança do Azure. A Microsoft utiliza estas informações de contacto para o contactar caso o Microsoft Security Response Center (MSRC) descobrir que os seus dados foram acedidos de forma ilícita ou não autorizada. Também tem opções para personalizar os alertas e as notificações de incidentes em diferentes serviços do Azure de acordo com as suas necessidades de resposta aos incidentes. 

- [Como definir o contacto de segurança do Centro de Segurança do Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Deteção e análise – criar incidentes baseados em alertas de alta qualidade 

**Orientação**: Certifique-se de que tem um processo para criar alertas de alta qualidade e medir a sua qualidade. Isto permite-lhe aprender lições de incidentes passados e priorizar alertas para os analistas, para evitar que o tempo desperdiçado processe falsos alertas positivos. Construa alertas de alta qualidade com base nas suas experiências de incidentes passados, fontes e ferramentas comunitárias validadas projetadas para gerar e limpar alertas com registo e correlação para diversas fontes de alerta. 

O Azure Security Center fornece alertas de alta qualidade em muitos ativos da Azure. Pode utilizar o conector de dados do Centro de Segurança para transmitir os alertas ao Azure Sentinel. Crie regras avançadas de alerta com a Azure Sentinel para gerar incidentes automáticos para uma investigação. 

Exporte os seus alertas e recomendações do Centro de Segurança para o Azure Sentinel com a funcionalidade de exportação para ajudar a identificar riscos para os recursos da Azure. Recomenda-se a criação de um processo de exportação de alertas e recomendações de forma automatizada para a segurança contínua.

- [Como configurar a exportação](../security-center/continuous-export.md)

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Deteção e análise – investigar incidentes

**Orientação**: Certifique-se de que os analistas podem consultar e utilizar diversas fontes de dados para construir uma visão completa da atividade que ocorreu enquanto investiga potenciais incidentes. Devem ser recolhidos diversos tipos de registos para rastrear as atividades de um potencial atacante através da cadeia de morte para evitar pontos cegos.  Também garanta que insights e aprendizagens são capturados para referência histórica.  

As origens de dados para investigação incluem as origens de registos centralizadas que já estão a ser recolhidas pelos serviços dentro do âmbito e pelos sistemas em execução, mas podem também incluir:

- Dados de rede – utilize os registos de fluxos dos grupos de segurança de rede, o Observador de Rede do Azure e o Azure Monitor para capturar os registos de fluxos de rede e outras informações analíticas. 

- Instantâneos dos sistemas em execução: 

    - Utilize a capacidade de instantâneos das Máquinas Virtuais do Azure para criar um instantâneo do disco do sistema em execução. 

    - Escolha a capacidade de despejo de memória nativa do sistema operativo para criar uma imagem da memória do sistema de funcionamento.

    - Utilize a funcionalidade instantânea dos serviços Azure ou uma capacidade de software de terceiros para criar instantâneos dos sistemas de execução.

O Azure Sentinel disponibiliza uma grande quantidade de análises de dados em praticamente qualquer origem de registos e um portal de gestão de casos para gerir o ciclo de vida completo dos incidentes. As informações de inteligência durante uma investigação podem ser associadas a um incidente para fins de monitorização e reporte. 

- [Criar um instantâneo de um disco de uma máquina Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Criar um instantâneo de um disco de uma máquina Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Informações de diagnóstico e recolha da captura da memória do Suporte do Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigar incidentes com o Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Deteção e análise – priorizar incidentes

**Orientação**: Dê contexto aos analistas quanto aos incidentes em que se devem concentrar primeiro tendo por base a gravidade dos alertas e a sensibilidade dos ativos. Use a gravidade atribuída a cada alerta do Centro de Segurança Azure para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança tem na descoberta ou na análise utilizada para emitir o alerta, bem como no nível de confiança em que havia uma intenção maliciosa por trás da atividade que levou ao alerta.

Além disso, marque os recursos com etiquetas e crie um sistema de nomenclatura para identificar e categorizar os recursos do Azure, especialmente aqueles que processam dados confidenciais.  É da sua responsabilidade priorizar a remediação dos alertas de acordo com a criticalidade dos recursos do Azure e o ambiente em que os incidentes ocorreram.

- [Alertas de segurança no Centro de Segurança do Azure](../security-center/security-center-alerts-overview.md)

- [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/management/tag-resources.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contenção, erradicação e recuperação – automatizar o processamento dos incidentes

**Orientação**: Automatize tarefas manuais repetitivas para acelerar o tempo de resposta e reduzir a carga dos analistas. As tarefas manuais são mais morosas e atrasam todos os incidentes, reduzindo o número daqueles com que um analista pode lidar. Estas tarefas também aumentam a fadiga dos analistas, o que aumenta o risco de erro humanos e provoca atrasos, bem como degrada a capacidade de aqueles se concentrarem em tarefas complexas. Utilize as funcionalidades de automatização de fluxos de trabalho do Centro de Segurança do Azure e do Azure Sentinel para acionar automaticamente ações ou executar um manual de procedimentos para responder aos alertas de segurança recebidos. O manual de procedimentos efetua ações, como o envio de notificações, a desativação de contas e o isolamento de redes problemáticas. 

- [Configurar a automatização dos fluxos de trabalho no Centro de Segurança](../security-center/workflow-automation.md)

- [Configurar respostas automatizadas a ameaças no Centro de Segurança do Azure](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configurar respostas automatizadas a ameaças no Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="posture-and-vulnerability-management"></a>Gestão da Postura e da Vulnerabilidade

*Para obter mais informações, veja [Referência de Segurança do Azure: Gestão da Postura e da Vulnerabilidade](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: Estabelecer configurações seguras para recursos computacional

**Orientação**: O Azure Stack Edge oferece suporte para criar configurações seguras para as máquinas virtuais locais que são criadas pelos clientes. Recomenda-se vivamente que a Microsoft disponibilize diretrizes para estabelecer linhas de base de segurança, ao mesmo tempo que cria máquinas virtuais locais,

- [Faça o download das linhas de base de segurança incluídas no kit de ferramentas de conformidade de segurança](/windows/security/threat-protection/windows-security-baselines)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: Manter configurações seguras para recursos computacional

**Orientação**: O Azure Stack Edge não oferece qualquer suporte para sustentar configurações seguras para as máquinas virtuais locais que foram criadas pelos clientes. Recomenda-se vivamente que os clientes utilizem os kits de ferramentas de conformidade de segurança (SCT) para ajudar a manter configurações seguras para os seus recursos computativos.

O sistema operativo anfitrião e as máquinas virtuais geridas pelo Azure Stack Edge mantêm as suas configurações de segurança. 

- [Linhas de base de segurança do Windows](/windows/security/threat-protection/windows-security-baselines#using-security-baselines-in-your-organization)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: Armazenar de forma segura sistema operativo personalizado e imagens de contentores

**Orientação**: Os sistemas operativos hospedeiros e as máquinas virtuais geridas pelo Azure Stack Edge são armazenados de forma segura. 

Os clientes podem trazer as suas próprias imagens de máquinas virtuais e contentores e são responsáveis pela sua gestão segura.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Remedeie as vulnerabilidades do software de forma rápida e automática

**Orientação**: O Azure Stack Edge fornece atualizações regulares de patch e alerta os clientes quando tais atualizações estão disponíveis para remediar vulnerabilidades. É da responsabilidade do cliente manter os seus dispositivos e máquinas virtuais (criados por eles) atualizados com as mais recentes correções.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Realizar simulações de ataques regulares

**Orientação**: Conforme necessário, faça testes de penetração ou atividades de "equipa de ataque" nos seus recursos do Azure e assegure a remediação de todas as descobertas de segurança críticas.
Para ter a certeza de que os seus testes de penetração não infringem as políticas da Microsoft, siga as Regras de Interação para Testes de Penetração da Microsoft Cloud. Utilize a estratégia e a execução de "Equipas de Ataque" e os testes de penetração no local em direto da Microsoft na infraestrutura, nos serviços e nas aplicações cloud geridas pela Microsoft.

- [Testes de Penetração no Azure](../security/fundamentals/pen-testing.md)

- [Regras de Interação para os Testes de Penetração](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- ["Equipa de Ataque" da Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="endpoint-security"></a>Segurança de ponto final

*Para mais informações, consulte o [Benchmark de Segurança Azure: Endpoint Security](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Utilizar deteção e resposta de ponto final (EDR)

**Orientação**: A azure Stack Edge não suporta diretamente a deteção e resposta do ponto final (EDR). No entanto, pode recolher um pacote de suporte e recuperar registos de auditoria. Estes registos podem então ser analisados para verificar se há atividades anómalas. 

- [Recolher pacote de suporte para dispositivo Azure Stack Edge](azure-stack-edge-gpu-troubleshoot.md#collect-support-package)

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Utilize software anti-malware moderno gerido centralmente

**Orientação**: Azure Stack Edge utiliza o Controlo de Aplicações do Windows Defender (WDAC) com uma política rígida de integridade de código (CI) que apenas permite executar aplicações e scripts pré-determinados. O anti-malware de proteção em tempo real (RTP) do Windows Defender também está ativado. O cliente pode optar por executar anti-malware nos VMs computacional que criam para funcionar localmente no dispositivo Azure Stack Edge.

**Monitorização do Centro de Segurança do Azure**: Não disponível atualmente

**Responsabilidade**: Cliente

## <a name="backup-and-recovery"></a>Cópia de Segurança e Recuperação

*Para obter mais informações, veja [Referência de Segurança do Azure: Cópia de Segurança e Recuperação](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Garantir backups automáticos regulares

**Orientação**: Recomenda-se a cópia de segurança periódica do seu dispositivo Azure Stack Edge e pode ser realizado com a Azure Backup e outras soluções de proteção de dados de terceiros para proteger os dados contidos nas máquinas virtuais implantadas no dispositivo. 

Soluções de proteção de dados de terceiros, como Coesity, Commvault e Veritas também podem fornecer uma solução de backup para os dados nas ações locais de SMB ou NFS, . 

Informações adicionais estão disponíveis nos links referenciados.

- [Prepare-se para uma falha do dispositivo](azure-stack-edge-gpu-prepare-device-failure.md)

- [Proteger ficheiros e pastas em VMs](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="br-2-encrypt-backup-data"></a>BR-2: Encriptar dados de backup

**Orientação**: Certifique-se de que as suas cópias de segurança estão protegidas contra ataques. Isto deve incluir encriptação das cópias de segurança para proteger contra a perda de confidencialidade.  Para mais informações, consulte a sua solução de reserva de escolha para mais detalhes.

- [Proteger dados em ações locais edge](azure-stack-edge-gpu-prepare-device-failure.md#protect-data-in-edge-local-shares)

- [Proteger ficheiros e pastas em máquinas virtuais](azure-stack-edge-gpu-prepare-device-failure.md#protect-files-and-folders-on-vms)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validar todas as cópias de segurança, incluindo as chaves geridas pelo cliente

**Orientação**: Execute periodicamente a restauração de dados das suas cópias de segurança. 

- [Procedimentos de recuperação para Azure Stack Edge](azure-stack-edge-gpu-recover-device-failure.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4: Mitigar o risco de perda de chaves

**Orientação**: Certifique-se de que todas as cópias de segurança do Azure Stack Edge, incluindo quaisquer chaves geridas pelo cliente, estão protegidas de acordo com as melhores práticas organizacionais. O seu dispositivo Azure Stack Edge está associado a uma conta de Armazenamento Azure, utilizada como repositório de destino para os seus dados em Azure. 

O acesso à conta Azure Storage é controlado por subscrições da Azure e pelas duas chaves de acesso ao armazenamento de 512 bits associadas a essa conta de armazenamento. Uma das teclas de acesso é utilizada para efeitos de autenticação quando o dispositivo Azure Stack Edge acede à conta de armazenamento. A outra chave é guardada reservada para a rotação periódica da chave. Certifique-se de que as melhores práticas de segurança são utilizadas para a rotação das chaves.

- [Proteja os dados do dispositivo Azure Stack Edge nas contas de armazenamento do Azure](azure-stack-edge-pro-r-security.md#protect-data-in-storage-accounts)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="governance-and-strategy"></a>Governação e Estratégia

*Para obter mais informações, veja [Referência de Segurança do Azure: Governação e Estratégia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir a gestão dos ativos e a estratégia de proteção de dados 

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para a monitorização e proteção contínua dos sistemas e dos dados. Priorize a descoberta, a avaliação, a proteção e a monitorização de dados e sistemas críticos para a empresa. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Norma para a classificação de dados, de acordo com os riscos da atividade

-   Visibilidade da organização de segurança para os riscos e inventário de ativos 

-   Aprovação da organização de segurança dos serviços do Azure a utilizar 

-   Segurança dos ativos ao longo do ciclo de vida

-   Estratégia de controlo de acesso obrigatória, de acordo com a classificação dos dados organizacionais

-   Utilização de capacidades de proteção de dados de terceiros e nativas do Azure

-   Requisitos de encriptação de dados para casos de utilização de dados em trânsito e inativos

-   Normas criptográficas adequadas

Para obter mais informações, veja as seguintes referências:
- [Recomendação de Arquitetura de Segurança do Azure - Armazenamento, dados e encriptação](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Noções Básicas da Segurança do Azure - Segurança, encriptação e armazenamento de dados do Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Melhores práticas de segurança e encriptação de dados do Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Referência de Segurança do Azure - Gestão de ativos](../security/benchmarks/security-controls-v2-asset-management.md)

- [Referência de Segurança do Azure - Proteção de dados](../security/benchmarks/security-controls-v2-data-protection.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir a estratégia de segmentação da empresa 

**Orientação**: Estabeleça uma estratégia ao nível de toda a empresa para segmentar o acesso aos ativos através de uma combinação de controlos de identidade, rede, aplicações, subscrições, grupos de gestão, entre outros.

Equilibre cuidadosamente a necessidade de separação da segurança com a necessidade de permitir o funcionamento diário dos sistemas que têm de comunicar entre si e aceder a dados.

Certifique-se de que a estratégia de segmentação está implementada de forma consistente em todos os tipos de controlos, incluindo segurança de rede, modelos de identidade e acesso e modelos de permissão/acesso a aplicações e controlos de processos humanos.

- [Orientação para a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação para a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir a estratégia de gestão da postura de segurança

**Orientação**: Meça e mitigue continuamente os riscos para os seus ativos individuais e para o ambiente em que estão alojados. Dê prioridade aos ativos de valor alto e a superfícies de ataque muito expostas, como aplicações publicadas, pontos de entrada e saída de rede, pontos finais de utilizador e administrador, etc.

- [Referência de Segurança do Azure - Gestão da postura e das vulnerabilidades](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar as funções, as responsabilidades e as responsabilizações na organização

**Orientação**: Certifique-se de que documenta e comunica uma estratégia clara para as funções e responsabilidades na sua organização de segurança. Estabeleça prioridades ao indicar de forma clara a responsabilização quanto às decisões de segurança, explicando o modelo de responsabilização partilhada a todos e explicando às equipas técnicas a tecnologia para proteger a cloud.

- [Melhor Prática de Segurança do Azure 1 – Pessoas: Explicar às Equipas o Percurso da Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor Prática de Segurança do Azure 2 – Pessoas: Explicar às Equipas a Tecnologia de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor Prática de Segurança do Azure 3 – Processo: Atribuir Responsabilização Quanto às Decisões de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir uma estratégia de segurança de rede

**Orientação**: Crie uma abordagem à segurança de rede do Azure como parte da estratégia de controlo de acesso de segurança geral da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Gestão centralizada da rede e responsabilidade quanto à segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação empresarial

-   Estratégia de remediação em diferentes cenários de ameaças e ataques

-   Estratégia de entrada e saída do edge da Internet

-   Estratégia de interconectividade entre a cloud híbrida e o ambiente no local

-   Artefactos de segurança de rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para obter mais informações, veja as seguintes referências:
- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Referência de Segurança do Azure - Segurança de Rede](../security/benchmarks/index.yml)

- [Descrição geral da segurança de rede do Azure](../security/fundamentals/network-overview.md)

- [Estratégia de arquitetura da rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir a estratégia de identidades e acessos privilegiados

**Orientação**: Crie uma abordagem às identidades e aos acessos privilegiados do Azure como parte da estratégia de controlo de acesso de segurança geral da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Um sistema de identidades e autenticação centralizado e respetiva interconectividade com outros sistemas de identidades internas e externas

-   Métodos de autenticação fortes em diferentes casos de utilização e condições

-   Proteção de utilizadores com muitos privilégios

-   Monitorização e processamento de atividades anómalas de utilizadores  

-   Processo de revisão e reconciliação de identidades e acessos dos utilizadores

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Gestão de identidades](../security/benchmarks/security-controls-v2-identity-management.md)

- [Referência de Segurança do Azure - Acesso privilegiado](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Descrição geral da segurança da gestão de identidades do Azure](../security/fundamentals/identity-management-overview.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta a ameaças e registos

**Orientação**: Crie uma estratégia de resposta a ameaças e registos para detetar e remediar rapidamente as ameaças e cumprir, em paralelo, os requisitos de conformidade. Dê prioridade a proporcionar aos analistas alertas de alta qualidade e experiências totalmente integradas, para que se possam focar nas ameaças em vez de se focarem na integração e passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   As funções e as responsabilidades da organização quanto às operações de segurança (SecOps) 

-   Processo de resposta a incidentes bem definido e alinhado com as normas da agência norte-americana NIST ou outro framework da indústria 

-   Captura e retenção de registos para suportar a deteção de ameaças, a resposta a incidentes e as necessidades de conformidade

-   Visibilidade centralizada e correlação de informações sobre ameaças, mediante a utilização de SIEM, das capacidades nativas do Azure e de outras origens 

-   Plano de comunicação e notificação com os seus clientes, fornecedores e partes interessadas públicas

-   Utilização de plataformas nativas do Azure e de terceiros para processamento de incidentes, como registo e deteção de ameaças, análises forenses e remediação e erradicação de ataques

-   Processos para lidar com incidentes e atividades pós-incidentes, como lições aprendidas e retenção de provas

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Registos e deteção de ameaças](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Referência de Segurança do Azure - Resposta a incidentes](../security/benchmarks/security-controls-v2-incident-response.md)

- [Melhor Prática de Segurança do Azure 4 – Processo. Atualizar os Processos de Resposta a Incidentes para a Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, registos e guia de decisão de relatórios](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="next-steps"></a>Passos seguintes

- Veja a [Descrição geral da Referência de Segurança do Azure v2](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)
