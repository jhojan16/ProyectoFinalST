
Vagrant.configure("2") do |config|

config.vm.define :haproxy do |haproxy|
haproxy.vm.box = "jhojanespinosa/box"
haproxy.vm.network :private_network, ip: "192.168.50.5"
haproxy.vm.hostname = "haproxy"
end

config.vm.define :cliente do |cliente|
cliente.vm.box = "jhojanespinosa/box"
cliente.vm.network :private_network, ip: "192.168.50.6"
cliente.vm.hostname = "cliente"
end

config.vm.define :maquina do |maquina|
maquina.vm.box = "jhojanespinosa/box"
maquina.vm.network :private_network, ip: "192.168.50.7"
maquina.vm.hostname = "maquina"
end
end
