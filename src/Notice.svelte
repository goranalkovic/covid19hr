<script>
  import Card, {
    Content,
    PrimaryAction,
    Media,
    MediaContent,
    Actions,
    ActionButtons,
    ActionIcons,
  } from "@smui/card";
  import Button, { Label } from "@smui/button";
  import { text } from "svelte/internal";
</script>

<section
  style="display: flex; flex-direction: column; align-items: center; margin:
  40px 0;">

  <Card style="width: 480px; max-width: 80vw;" variant="outlined">
    <Content>
      <h2
        class="mdc-typography--headline6"
        style="margin: 0; text-align: left;">
        Izvor podataka
      </h2>
      <!-- <p>
        Ovi su podaci prikupljeni prema informacijama sa sjednica Nacionalnog
        stožera, pa je moguće da se u prijenosu podataka pojavila greška.
        Konačne i provjerene podatke uvijek možete dobiti na službenoj stranici
        Vlade.
      </p> -->
      <p>
        Podaci se dohvaćaju sa službenih izvora Nacionalnog stožera civilne
        zaštite.
      </p>
    </Content>
    <Actions>
      <ActionButtons>
        <Button href="https://koronavirus.hr" target="_blank">
          <Label>Službene stranice Stožera</Label>
        </Button>
        <Button
          href="https://www.koronavirus.hr/json/?action=podaci_zadnji"
          target="_blank">
          <Label>Izvor podataka</Label>
        </Button>
      </ActionButtons>

    </Actions>
  </Card>
</section>
