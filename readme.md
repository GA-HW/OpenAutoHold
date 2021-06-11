# OpenAutoHold

Auto Hold is great - lets add it to more things

## What is "Auto Hold"?
Auto Hold is a feature in the Gucci multimeters that automagically freezes the display once a reading has stabilised. Most multimeters have "Data Hold", which is a near-useless feature where you press a button to freeze the display on a reading, requiring you to have two hands for the probes, and a third to press the button.

## Cool, I want it - How do I get it?
Glad you asked - first we'll need to have a look at how multimeters are built
Basically there are a few different ways a manufacturer can build their multimeter. At the core of most multimeters are multimeter chipsets. These generally provide voltage references, analogue to digital conversion, range switching, and other critical things. Often they will also drive the display and deal with button presses. Below are some of the different ways that they can be used:

#### All-In-One Control
This is the most common method. Basically, the chip does everything, including the analogue frontend and display/controls. The multimeter manufacturer just buys the one chip for core functions (potentially adding TRMS conversion chips, and logic components to teak the controls), and then adds the passive components, and plumbs in the features they need. They often can do more than the manufacturer has allowed that multimeter to do, and they leave some bits out for product differentiation. Pins for buttons controlling more premium features, or outputting to display segments not present (or whatever) can be left unused. They are generally either configured with some pins being driven low or high to turn things on or off, or can have an external EEPROM holding calibration data and bits controlling features. These are generally ripe for hacking, with new features being able to be added, and configuration (backlight time) and calibration data tweaked. There are only a handful of chipsets, so hacks for one should work for many others.

#### Frontend Only
The chipset only provides analogue frontend features (analogue to digital conversion, range switching.etc), and sends data over SPI (or whatever) to a microprocessor that does the user functions (buttons, display.etc). These can either be frontend-only chips, or some chipsets can be used as a whole-system chipset, but still have SPI links to allow them to be used in this manner. Depending on the individual multimeter, you might be able to achieve more or less. If MCU software can be reverse engineered or just simply rewritten, then you've got scope - otherwise you might still be able to control features with pin-driving or an EEPROM if present, though features may be controlled from the MCU. However, this does all mean that you have an SPI (or whatever) link to mess around with... These meters may also have MCUs that do datalogging or graphing functions, with extra memory, and there are scopemeters that have the chipset with an MCU and an FPGA for fast acquisition.

#### Custom Chipsets
The more premium manufacturers may also specify custom chipsets, or just flat out make their own. Lack of datasheets.etc here may provide a problem for hacking, or features not needed may just be not present in the silicon or permanently disabled. For now at least, this is out of scope :-(

#### No Chipset
Sometimes a manufacturer will actually just not use a chipset, and use MCUs with high-quality ADCs, or MCUs with discreet ADCs, and whatever other bits they feel like. This is generally pretty rare, although some brands do it a fair bit (eg Hioki). Since it's all separate, there's potentially more scope for hacking, although needing a lot of per-product work to reverse engineer bits, rewrite firmware, and generally deal with the fact that you've got to go into a massive load of things (more than just a couple of EEPROM settings) for each unique meter - and they'll likely be very different. Again, for now at least, out of scope :-(

## So how do we get Auto Hold?
The basic idea is simple:
1. We get data out
    * The chipset may already output for a serial connection that we can listen to
    * We may be able to hack the multimeter to get the chipset to output serial
    * There may be a SPI link for use as a frontend only that we can listen to
2. We read the reading and look for a stable measurement to hold
3. We press hold
    * We can actually "press" the hold button by poking at a pin (there's probably also a via to solder to from the hold button on the other side of the board)
    * We may be able to send a hold signal over the SPI link if present
4. Profit

Basically, reflash an EEPROM, poke at some pins, or whatever, to get data to a MCU that we'll add, and then do something to press hold. Simple.

# **DISCLAIMER!!!**
Fucking around with the inside of a multimeter **will** adversely affect its safety protections. Don't modify it and then assume that it's safe to go probing dangerous things. This is all academic/for entertainment - I'm assuming that you won't be doing this unless you both know what you're doing, and have the common sense to not then use a modified multimeter for anything that could potentially be even remotely dangerous.