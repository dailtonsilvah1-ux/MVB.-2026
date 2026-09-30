<!DOCTYPE html>
<html lang="pt">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Minha Localização</title>

  <!-- Mapa -->
  <link rel="stylesheet"
        href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css">

  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin: 0;
      padding: 25px;
    }

    button {
      padding: 12px 20px;
      font-size: 16px;
      border: none;
      border-radius: 8px;
      cursor: pointer;
    }

    #resultado {
      margin: 20px 0;
    }

    #map {
      height: 450px;
      width: 100%;
      max-width: 800px;
      margin: auto;
    }
  </style>
</head>

<body>

  <h1>📍 Minha localização</h1>

  <p>Este site mostra a localização do dispositivo após autorização.</p>

  <button onclick="localizar()">
    Encontrar minha localização
  </button>

  <div id="resultado">
    Aguardando...
  </div>

  <div id="map"></div>

  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

  <script>
    let mapa;
    let marcador;

    function localizar() {

      if (!navigator.geolocation) {
        document.getElementById("resultado").innerText =
          "Este navegador não suporta geolocalização.";
        return;
      }

      document.getElementById("resultado").innerText =
        "Obtendo localização...";

      navigator.geolocation.getCurrentPosition(
        mostrarLocalizacao,
        erro,
        {
          enableHighAccuracy: true,
          timeout: 15000,
          maximumAge: 0
        }
      );
    }

    function mostrarLocalizacao(posicao) {

      const latitude = posicao.coords.latitude;
      const longitude = posicao.coords.longitude;
      const precisao = posicao.coords.accuracy;

      document.getElementById("resultado").innerHTML =
        "Latitude: " + latitude.toFixed(6) +
        "<br>Longitude: " + longitude.toFixed(6) +
        "<br>Precisão aproximada: " +
        Math.round(precisao) + " metros";

      if (!mapa) {

        mapa = L.map("map").setView(
          [latitude, longitude],
          17
        );

        L.tileLayer(
          "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
          {
            attribution: "© OpenStreetMap contributors"
          }
        ).addTo(mapa);
      } else {
        mapa.setView([latitude, longitude], 17);
      }

      if (marcador) {
        marcador.setLatLng([latitude, longitude]);
      } else {
        marcador = L.marker(
          [latitude, longitude]
        ).addTo(mapa);

        marcador.bindPopup(
          "📍 A tua localização"
        ).openPopup();
      }
    }

    function erro(e) {

      if (e.code === 1) {
        document.getElementById("resultado").innerText =
          "Permissão de localização recusada.";
      }

      if (e.code === 2) {
        document.getElementById("resultado").innerText =
          "Não foi possível encontrar a localização.";
      }

      if (e.code === 3) {
        document.getElementById("resultado").innerText =
          "A localização demorou demasiado tempo.";
      }
    }
  </script>

</body>
</html>
