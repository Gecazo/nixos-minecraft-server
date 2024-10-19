### Simple minecraft server example
```nix
  { config, pkgs, ... }:
    
    let
      # Define the Minecraft server version
      minecraft-server-version = "1.20.1"; # Replace with the desired version
    in
    {
  # Enable systemd service for the Minecraft server
      
    services.minecraft-server = {
    
      enable = true;
      eula = true;  # You must agree to the EULA (https://account.mojang.com/documents/minecraft_eula)
      user = "minecraft";  # User under which the server will run
      group = "minecraft"; # Group for permissions
      
      # Configure where to store Minecraft world data and settings
      worldName = "world";
      dataDir = "/var/minecraft-server";  # Location for the server files
  
      # Specify the server jar file and version
      jar = "${pkgs.minecraft-server}/libexec/server.jar";
      
      # Additional JVM options (optional)
      jvmOpts = [
        "-Xmx4G"  # Set max memory allocation for JVM (adjust as needed)
        "-Xms1G"  # Set initial memory allocation
      ];
  
  # Configure additional Minecraft server options
      extraOptions = {
        # Example options (you can add more server.properties options here)
        "difficulty" = "normal";
        "gamemode" = "survival";
        "white-list" = "false";
        "online-mode" = "true";  #x Enable online authentication
        "pvp" = "true";
      };
    };
  
  # Create the user and group for the Minecraft server
    users.users.minecraft = {
      isNormalUser = true;
      description = "Minecraft Server User";
      home = "/var/minecraft-server";
      createHome = true;
      shell = pkgs.bash;
      group = "minecraft";
    };
  
  # Optionally open the necessary port in the firewall
    networking.firewall.allowedTCPPorts = [25565];  # Minecraft default port
  
  # Enable Java runtime if not already enabled
    environment.systemPackages = with pkgs; [
      pkgs.minecraft-server  # The Nix package for Minecraft server
      pkgs.openjdk17  # Java 17 is required for Minecraft 1.18 and later
    ];
  }
