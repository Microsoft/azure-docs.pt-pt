---
title: 'Pré-visualização: Lançamento confiável para VMs Azure'
description: Saiba mais sobre o lançamento fidedigno para máquinas virtuais Azure.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 5e500eca601d21f106dbe31236e9b5c2aa76b0d2
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801932"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Lançamento confiável para máquinas virtuais Azure (pré-visualização)

A Azure oferece um lançamento fidedigno como uma forma perfeita de melhorar a segurança da [geração 2](generation-2.md) VMs. O lançamento de confiança protege contra técnicas de ataque avançadas e persistentes. O lançamento de confiança é composto por várias tecnologias de infraestrutura coordenadas que podem ser ativadas de forma independente. Cada tecnologia fornece outra camada de defesa contra ameaças sofisticadas.

> [!IMPORTANT]
> O lançamento fidedigno requer a criação de novas máquinas virtuais. Não é possível permitir o lançamento fidedigno em máquinas virtuais existentes que foram inicialmente criadas sem ela.
>
> O lançamento de confiança está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. 
>
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="benefits"></a>Benefícios 

- Coloque de forma segura máquinas virtuais com carregadores de arranque verificados, núcleos de SO e controladores.
- Proteja seguramente chaves, certificados e segredos nas máquinas virtuais.
- Obtenha insights e confiança de toda a integridade da cadeia de botas.
- Certifique-se de que as cargas de trabalho são confiáveis e verificáveis.

## <a name="public-preview-limitations"></a>Limitações da pré-visualização pública

**Suporte de tamanho**: Todos os tamanhos [de Geração 2](generation-2.md) VM, exceto:

- Série Lsv2 
- Série M 
- Série Mv2 
- Série NDv4 
- Série NVv4

**Suporte do SO**:
- Redhat Enterprise Linux 8.3
- SUSE 15 SP2
- Ubuntu 20.04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 Pro
- Windows 10 Enterprise
- Multi-sessão empresarial do Windows 10

**Regiões:** 
- E.U.A. Centro-Sul
- Europa do Norte

**Preços**: Sem custos adicionais para os preços em VM existentes.

**As seguintes funcionalidades não são suportadas nesta pré-visualização:**
- Backup
- Azure Site Recovery
- Galeria de Imagens Partilhada
- Disco EFÉMER OS
- Disco compartilhado
- Imagem gerida
- Azure Dedicated Host 

## <a name="secure-boot"></a>Bota segura

Na raiz do lançamento fidedigno está o Secure Boot para o seu VM. Este modo, que é implementado no firmware da plataforma, protege contra a instalação de rootkits e kits de arranque baseados em malware. O Secure Boot funciona para garantir que apenas os sistemas operativos assinados e os controladores podem arrancar. Estabelece uma "raiz de confiança" para a pilha de software no seu VM. Com o Arranque Seguro ativado, todos os componentes de arranque DE SO (carregador de arranque, kernel, kernel) devem ser assinados por editores de confiança. Tanto o Windows como o sistema de distribuição Linux suportam o Secure Boot. Se o Secure Boot não autenticar que a imagem foi assinada por uma editora de confiança, o VM não será autorizado a arrancar. Para obter mais informações, consulte [Arranque Seguro](/windows-hardware/design/device-experiences/oem-secure-boot).

## <a name="vtpm"></a>vTPM

O lançamento fidedigno também introduz vTPM para VMs Azure. Esta é uma versão virtualizada de um Módulo de [Plataforma Fidedigna](/windows/security/information-protection/tpm/trusted-platform-module-overview)de hardware, em conformidade com a especificação TPM2.0. Serve como um cofre seguro dedicado para chaves e medidas. O lançamento fidedigno fornece ao seu VM a sua própria instância TPM dedicada, funcionando num ambiente seguro fora do alcance de qualquer VM. O vTPM permite [a estação de atestação](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) medindo toda a cadeia de arranque do seu VM (UEFI, OS, sistema e controladores). 

O lançamento fidedigno utiliza o vTPM para realizar atetação remota pela nuvem. Isto é usado para controlos de saúde da plataforma e para tomar decisões baseadas em confiança. Como uma verificação de saúde, o lançamento fidedigno pode certificar criptograficamente que o seu VM foi iniciado corretamente. Se o processo falhar, possivelmente porque o seu VM está a executar um componente não autorizado, o Azure Security Center emitirá alertas de integridade. Os alertas incluem detalhes sobre quais os componentes que não passaram em verificações de integridade.

## <a name="virtualization-based-security"></a>Segurança baseada na virtualização

[A Segurança baseada na virtualização](/windows-hardware/design/device-experiences/oem-vbs) (VBS) utiliza o hipervisor para criar uma região de memória segura e isolada. O Windows utiliza estas regiões para executar várias soluções de segurança com maior proteção contra vulnerabilidades e explorações maliciosas. O lançamento fidedigno permite-lhe ativar a Integridade do Código hipervisor (HVCI) e a Guarda Credencial do Windows Defender.

O HVCI é uma poderosa mitigação do sistema que protege os processos do modo kernel do Windows contra a injeção e execução de código malicioso ou não verificado. Verifica os controladores e binários do modo kernel antes de funcionarem, evitando que ficheiros não assinados carreguem na memória. Isto garante que tal código executável não pode ser modificado uma vez que é permitido carregar. Para obter mais informações sobre VBS e HVCI, consulte [a Segurança Baseada na Virtualização (VBS) e a Integridade do Código Aplicado do Hipervisor (HVCI)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571).

Com lançamento fidedigno e VBS pode ativar a Guarda Credencial do Windows Defender. Esta funcionalidade isola e protege segredos para que apenas o software privilegiado do sistema possa aceder aos mesmos. Ajuda a impedir o acesso não autorizado a segredos e ataques de roubo de credenciais, como ataques Pass-the-Hash (PtH). Para mais informações, consulte [a Guarda Credencial.](/windows/security/identity-protection/credential-guard/credential-guard)


## <a name="security-center-integration"></a>Integração do Centro de Segurança

O lançamento fidedigno está integrado no Azure Security Center para garantir que os seus VMs estão devidamente configurados. O Azure Security Center avaliará continuamente os VM compatíveis e emitirá recomendações relevantes.

- **Recomendação para ativar o Arranque Seguro** - Esta Recomendação aplica-se apenas aos VMs que suportam o lançamento fidedigno. O Azure Security Center identificará VMs que podem ativar o Secure Boot, mas desativá-lo. Emitirá uma recomendação de baixa gravidade para o permitir.
- **Recomendação para ativar vTPM** - Se o seu VM tiver ativado vTPM, o Azure Security Center pode usá-lo para executar o Attestation de Hóspedes e identificar padrões de ameaça avançados. Se o Azure Security Center identificar VMs que suportam o lançamento fidedigno e têm vTPM desativado, emitirá uma recomendação de baixa gravidade para o permitir. 
- **Avaliação de saúde do atestado** - Se o seu VM tiver o vTPM ativado, uma extensão do Centro de Segurança Azure pode validar remotamente que o seu VM foi iniciado de forma saudável. Isto é conhecido como atestado remoto. O Azure Security Center emite uma avaliação, indicando o estado do atestado remoto.

## <a name="azure-defender-integration"></a>Integração do Azure Defender

Se os seus VMs estiverem devidamente configurados com lançamento fidedigno, o Azure Defender pode detetá-lo e alertá-lo para problemas de saúde em VM.

- **Alerta para falha no atestado VM** - O Azure Defender realizará periodicamente atestado nos seus VMs. Isto também acontece depois das suas botas VM. Se o atestado falhar, irá desencadear um alerta de gravidade média.
    O atestado em VM pode falhar pelas seguintes razões:
    - A informação atesta, que inclui um tronco de bota, desvia-se de uma linha de base fidedigna. Isto pode indicar que os módulos não fided os falsos foram carregados, e o SO pode estar comprometido.
    - A citação do atestado não pôde ser verificada a partir do vTPM do VM atestado. Isto pode indicar que o malware está presente e pode estar a intercetar o tráfego para o vTPM.
    - A extensão do Attestation no VM não está a responder. Isto pode indicar um ataque de negação de serviço por malware ou um administrador de SISTEMA.

    > [!NOTE]
    >  Este alerta está disponível para VMs com vTPM ativado e a extensão atestado instalada. O Arranque Seguro deve ser ativado para a atestado passar. A atestado falhará se o Arranque Seguro estiver desativado. Se tiver de desativar o Secure Boot, pode suprimir este alerta para evitar falsos positivos.

- **Alerta para o módulo Kernel Linux Não Fidedigno** - Para um lançamento fidedigno com uma bota segura ativada, é possível que um VM arranque mesmo que um condutor de kernel falhe na validação e esteja proibido de carregar. Se isso acontecer, o Azure Defender emitirá um alerta de baixa gravidade. Embora não haja ameaça imediata, porque o condutor não é mal-educado, estes acontecimentos devem ser investigados. Considere o seguinte:
    - Que condutor falhou? Conheço este motorista e espero que seja carregado?
    - Esta é a versão exata do condutor que estou à espera? Os binários do condutor estão intactos? Se este é um motorista de terceira parte, o vendedor passou nos testes de conformidade do SO para o assinar?


## <a name="faq"></a>FAQ

Perguntas frequentes sobre lançamento de confiança.

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>Por que devo usar lançamento de confiança? Contra o que é que confia na guarda de lançamento?

Guardas de lançamento confiáveis contra kits de arranque, rootkits e malware de nível kernel. Estes tipos sofisticados de malware são executados em modo kernel e permanecem escondidos dos utilizadores. Por exemplo:
- Rootkits firmware: estes kits substituem o firmware do BIOS da máquina virtual, para que o rootkit possa começar antes do SISTEMA. 
- Kits de arranque: estes kits substituem o bootloader do SO de modo que a máquina virtual carregue o kit de arranque antes do SO.
- Kits de raiz kernel: estes kits substituem uma parte do núcleo de SO para que o rootkit possa começar automaticamente quando o SISTEMA estiver carregado.
- Kits de raiz do condutor: estes kits fingem ser um dos condutores de confiança que o SO utiliza para comunicar com os componentes da máquina virtual.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>Quais são as diferenças entre arranque seguro e bota medida?

Na cadeia de arranque segura, cada passo no processo de arranque verifica uma assinatura criptográfica dos passos seguintes. Por exemplo, o BIOS verificará uma assinatura no carregador, e o carregador verificará as assinaturas em todos os objetos de núcleo que carrega, e assim por diante. Se algum dos objetos estiver comprometido, a assinatura não corresponderá e o VM não arrancará. Para obter mais informações, consulte [Arranque Seguro](/windows-hardware/design/device-experiences/oem-secure-boot). O arranque medido não interrompe o processo de arranque, mede ou calcula o haxixe dos objetos seguintes na cadeia e armazena os hashes nos Registos de Configuração da Plataforma (PCRs) no vTPM. Os registos de arranque medidos são utilizados para a monitorização da integridade do arranque.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>O que acontece quando uma falha de integridade é detetada?

O lançamento confiável para máquinas virtuais Azure é monitorizado para ameaças avançadas. Se tais ameaças forem detetadas, será acionado um alerta. Os alertas só estão disponíveis no [Standard Tier](../security-center/security-center-pricing.md) of Azure Security Center.
O Centro de Segurança Azure realiza periodicamente atestado. Se o atestado falhar, será acionado um alerta de gravidade média. Atestado de lançamento fidedigno pode falhar pelas seguintes razões: 
- As informações atestadas, que incluem um registo da Base de Computação Fidedigna (TCB), desviam-se de uma linha de base fidedigna (como quando o Secure Boot está ativado). Isto pode indicar que os módulos não fided os falsos foram carregados e o SO pode estar comprometido.
- A citação do atestado não pôde ser verificada a partir do vTPM do VM atestado. Isto pode indicar que o malware está presente e pode estar a intercetar o tráfego para o TPM. 
- A extensão do atestado no VM não está a responder. Isto pode indicar um ataque de negação de serviço por malware ou um administrador de SISTEMA.

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>Como é que o lançamento de confiança é comparado com o VM blindado Hiper-V?
O VM blindado hiper-V está disponível apenas em Hiper-V. [O VM blindado hiper-V](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) é normalmente implantado em conjunto com o Tecido Protegido. Um Tecido Protegido é composto por um Serviço de Guardião anfitrião (HGS), um ou mais anfitriões protegidos, e um conjunto de VMs blindados. Os VMs blindados hiper-V destinam-se a ser utilizados em tecidos onde os dados e o estado da máquina virtual devem ser protegidos tanto dos administradores de tecido como do software não fidedquipado que possa estar a funcionar nos anfitriões Hyper-V. Por outro lado, o lançamento fidedigno pode ser implementado como uma máquina virtual autónoma ou conjuntos de escala de máquina virtual em Azure sem implantação e gestão adicionais de HGS. Todas as funcionalidades de lançamento fidedignas podem ser ativadas com uma simples alteração no código de implementação ou uma caixa de verificação no portal Azure.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>Como posso converter VMs existentes para lançamentos de confiança?
Para a Geração 2 VM, o caminho de migração para converter para lançamento fidedigno é direcionado para a disponibilidade geral (GA).

## <a name="next-steps"></a>Passos seguintes

Implementar um [VM de lançamento de confiança utilizando o portal](trusted-launch-portal.md).
