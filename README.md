🛡️ Maverick-Tor: Transparent Proxy for Arch Linux"Desenvolvido com a filosofia Maverick: Faça você mesmo, entenda o que roda."O Maverick-Tor é um script de roteamento Tor robusto, auditável e à prova de vazamentos, desenhado especificamente para usuários de Arch Linux e Archcraft.É uma alternativa minimalista e transparente ao Anonsurf e Nipe, focada em resolver as peculiaridades do Arch (como conflitos com systemd-resolved e links simbólicos de DNS) que frequentemente quebram outras ferramentas.🚀 Funcionalidades👻 Ghost DNS Strategy (V7): Utiliza uma técnica de "IP Fantasma" para contornar conflitos de DNS local, garantindo que 100% das requisições de nome sejam resolvidas pelo Tor.🔗 Symlink Safe: Detecta e preserva links simbólicos do /etc/resolv.conf, garantindo que o sistema volte exatamente ao estado original após o uso.🛑 IPv6 Kill Switch: Desabilita temporariamente a pilha IPv6 para prevenir vazamentos de identidade (já que o Tor suporta primariamente IPv4).🌐 System-wide Routing: Redireciona todo o tráfego TCP do sistema através da rede Onion.👁️ Auditável: Um único arquivo Bash. Sem binários ocultos, sem dependências de Python/Perl quebradas.🛠️ Guia de Instalação (Passo a Passo)Siga os passos abaixo rigorosamente para preparar seu sistema antes de rodar o script.Passo 1: Instalar DependênciasVocê precisa do pacote tor (o serviço) e iptables (o firewall). No Arch Linux/Archcraft, execute:sudo pacman -S tor iptables
Passo 2: Configurar o Tor (Crucial)O Tor padrão não aceita conexões transparentes nativamente. Você precisa abrir as portas de roteamento e DNS.Abra o arquivo de configuração do Tor:sudo nano /etc/tor/torrc
Vá até o final do arquivo e cole exatamente o bloco abaixo:# NOTA: Adicionar ao final de /etc/tor/torrc

# Configuração para Proxy Transparente (Maverick Script)
VirtualAddrNetworkIPv4 10.192.0.0/10
AutomapHostsOnResolve 1

# Porta para tráfego TCP (Navegação, SSH, etc)
TransPort 0.0.0.0:9040

# Porta para resolver DNS (Evita vazamentos)
DNSPort 0.0.0.0:5353
Salve e saia (Ctrl+O, Enter, Ctrl+X).Passo 3: Instalar o ScriptAgora, baixe e instale o Maverick-Tor:# 1. Clone este repositório
git clone [https://github.com/mavericksf/maverick-tor.git](https://github.com/mavericksf/maverick-tor.git)
cd maverick-tor

# 2. Torne o script executável
chmod +x maverick-tor

# 3. (Opcional) Mova para a pasta de binários para executar de qualquer lugar
sudo cp maverick-tor /usr/local/bin/
💻 UsoO script deve ser executado como root (sudo) para manipular o firewall e o DNS.▶️ Iniciar (Modo Anônimo)sudo maverick-tor start
Isso irá configurar o IP Fantasma, travar o arquivo DNS, aplicar regras de IPTables e reiniciar o serviço Tor.Verificação de funcionamento:curl [https://check.torproject.org/api/ip](https://check.torproject.org/api/ip)
Deve retornar {"IsTor":true, ...}.⏹️ Parar (Restaurar Internet Normal)sudo maverick-tor stop
Limpa o firewall, destrava o DNS, restaura o link simbólico original do systemd e reativa o IPv6.🔍 Como Funciona (A Estratégia "Ghost DNS")Muitas ferramentas falham no Arch Linux porque o systemd-resolved luta pelo controle do arquivo /etc/resolv.conf. O Maverick-Tor resolve isso enganando o sistema:O script configura o DNS do sistema para um IP inexistente na rede (ex: 10.0.0.242).Quando um programa tenta resolver um domínio, o Linux envia um pacote para esse IP fantasma através da interface de rede.O IPTables intercepta esse pacote "saindo" e reescreve o destino (DNAT) para 127.0.0.1:5353 (Porta DNS do Tor).O Tor resolve o nome anonimamente e devolve o IP.Isso evita conflitos de localhost e garante que o tráfego DNS seja sempre capturado pelo firewall.⚠️ Aviso Legal e SegurançaSegurança Operacional: Este script oculta seu IP, mas não protege contra Browser Fingerprinting ou scripts maliciosos se você usar um navegador inseguro. Recomenda-se o uso do Mullvad Browser ou LibreWolf enquanto o script estiver ativo.Google/Serviços: Espere bloqueios ou CAPTCHAs ao acessar serviços do Google, Cloudflare ou Bancos, pois IPs de saída do Tor são frequentemente listados em blocklists.Uso: Esta ferramenta é para fins educacionais e de privacidade pessoal.
