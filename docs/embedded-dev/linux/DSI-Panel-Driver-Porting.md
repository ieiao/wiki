# DSI-Panel-Driver-Porting

[DSI Panel Driver Porting](https://github.com/freedreno/freedreno/wiki/DSI-Panel-Driver-Porting)

This is a work-in-progress document describing how to decipher panel driver programming from downstream android dts files, in particular 3.10 based kernels.  I'm working off of the kernel for sony xperia devices, but (I think) other 3.10 based devices should be similar.

## Downstream Panel Drivers

Rather than having per-panel drivers that are decipherable, the downstream kernel uses a "meta" panel driver, with programming sequences taken from device tree (dts) files.  For example, the xperia z3 panel description for the auo novatek 1080p panel has:

        dsi_novatek_auo_1080_vid: somc,novatek_auo_1080p_video_panel {
                qcom,mdss-dsi-panel-name = "auo novatek 1080p vid";
                qcom,mdss-dsi-panel-controller = <&mdss_dsi0>;
                qcom,mdss-dsi-panel-type = "dsi_video_mode";
                qcom,mdss-dsi-panel-destination = "display_1";
                qcom,mdss-dsi-panel-framerate = <60>;
                qcom,mdss-dsi-virtual-channel-id = <0>;
                qcom,mdss-dsi-stream = <0>;
                qcom,mdss-dsi-panel-width = <1080>;
                qcom,mdss-dsi-panel-height = <1920>;
                qcom,mdss-dsi-h-front-porch = <56>;
                qcom,mdss-dsi-h-back-porch = <8>;
                qcom,mdss-dsi-h-pulse-width = <8>;
                qcom,mdss-dsi-h-sync-skew = <0>;
                qcom,mdss-dsi-v-back-porch = <8>;
                qcom,mdss-dsi-v-front-porch = <233>;
                qcom,mdss-dsi-v-pulse-width = <2>;
                qcom,mdss-dsi-h-left-border = <0>;
                qcom,mdss-dsi-h-right-border = <0>;
                qcom,mdss-dsi-v-top-border = <0>;
                qcom,mdss-dsi-v-bottom-border = <0>;
                qcom,mdss-dsi-bpp = <24>;
                qcom,mdss-dsi-underflow-color = <0x0>;
                qcom,mdss-dsi-border-color = <0>;
                somc,mdss-dsi-init-command = [15 01 00 00 00 00 02 FF E0
                                15 01 00 00 00 00 02 FB 01
                                15 01 00 00 00 00 02 B5 86
                                15 01 00 00 00 00 02 B6 77
                                15 01 00 00 00 00 02 B8 AD
                                15 01 00 00 00 00 02 FF 20
                                15 01 00 00 00 00 02 FB 01
                                15 01 00 00 00 00 02 10 04
                                15 01 00 00 00 00 02 FF 24
                                15 01 00 00 00 00 02 FB 01
                                15 01 00 00 00 00 02 C6 00
                                15 01 00 00 00 00 02 C5 32
                                15 01 00 00 00 00 02 92 92
                                15 01 00 00 00 00 02 FF 10
                                15 01 00 00 00 00 02 35 00
                                39 01 00 00 00 00 03 44 03 00
                                39 01 00 00 00 00 04 3B 03 30 06
                                15 01 00 00 01 00 02 BB 10
                                05 01 00 00 1E 00 01 11];
                qcom,mdss-dsi-on-command = [05 01 00 00 28 00 01 29];
                qcom,mdss-dsi-off-command = [15 01 00 00 00 00 02 FF 10
                                05 01 00 00 00 00 01 28
                                05 01 00 00 64 00 01 10];
                qcom,mdss-dsi-on-command-state = "dsi_lp_mode";
                qcom,mdss-dsi-off-command-state = "dsi_hs_mode";

                qcom,mdss-dsi-h-sync-pulse = <1>;
                qcom,mdss-dsi-traffic-mode = "non_burst_sync_event";
                qcom,mdss-dsi-bllp-eof-power-mode;
                qcom,mdss-dsi-bllp-power-mode;
                qcom,mdss-dsi-lane-0-state;
                qcom,mdss-dsi-lane-1-state;
                qcom,mdss-dsi-lane-2-state;
                qcom,mdss-dsi-lane-3-state;
                qcom,mdss-dsi-panel-timings = [E6 38 26 00 68 6E 2A 3C 2C 03 04 00];
                qcom,mdss-dsi-lp11-init;
                qcom,mdss-dsi-t-clk-post = <0x21>;
                qcom,mdss-dsi-t-clk-pre = <0x2B>;
                qcom,mdss-dsi-bl-min-level = <1>;
                qcom,mdss-dsi-bl-max-level = <4095>;
                qcom,mdss-brightness-max-level = <4095>;
                qcom,mdss-dsi-dma-trigger = "trigger_sw";
                qcom,mdss-dsi-mdp-trigger = "none";
                qcom,mdss-dsi-bl-pmic-control-type = "bl_ctrl_wled";
                qcom,mdss-dsi-pan-enable-dynamic-fps;
                qcom,mdss-dsi-pan-fps-update = "dfps_suspend_resume_mode";
                qcom,cont-splash-enabled;

                somc,driver-ic = <0>;
                somc,touch-reset-gpio = <&msmgpio 85 0>;
                somc,mdss-phy-size-mm = <64 114>;
                somc,mdss-dsi-lane-config = [00 c2 ef 00 00 00 00 01 ff
                                00 c2 ef 00 00 00 00 01 ff
                                00 c2 ef 00 00 00 00 01 ff
                                00 c2 ef 00 00 00 00 01 ff
                                00 02 45 00 00 00 00 01 97];
                somc,mdss-dsi-disp-on-in-hs = <1>;
                somc,mdss-dsi-wait-time-before-on-cmd = <150>;
                somc,lcd-id = <1>;
                somc,lcd-id-adc = <1236000 1395000>;
                somc,disp-en-on-post = <20>;
                somc,pw-on-rst-seq = <1 10>;
                somc,disp-en-off-post = <70>;
                somc,pw-off-rst-seq = <0 0>;
                somc,pw-down-period = <100>;

                somc,mdss-dsi-pre-uv-command = [23 01 00 00 00 00 02 B0 04];
                somc,mdss-dsi-uv-command = [06 01 00 00 00 00 01 DA
                        06 01 00 00 00 00 01 DB];
                somc,mdss-dsi-uv-param-type = <4>;
                somc,mdss-dsi-pcc-table-size = <225>;
                somc,mdss-dsi-pcc-table = <
                        0x00 0x01 0x38 0x3B 0x38 0x3B 0x5700 0x7000 0x8000
                        ... big blob ...
                        0xFF 0x00 0x00 0x3B 0x00 0x3B 0x8000 0x8000 0x8000>;
        };

## Upstream Panel Drivers

The upstream kernel, by comparison, has the common panel framework (see `drivers/gpu/drm/panel`).  The common panel framework allows for panel drivers to be written once, and shared between different SoC's, so it uses drm's `struct mipi_dsi_device` to abstract away the display driver implementation.  For simple panels that don't require custom programming sequences there is `panel-simple.c`, but that is unlikely to be applicable for the DSI panel in any phone/tablet.  Instead, for panels requiring custom programming sequences, a new panel driver is written.  For this document, I will use the example of the `panel-auo-novatek-1080p-vid.c` driver that I am writing for the AUO panel in my xperia z3.  (Note that sony multi-sources their panel drivers so not all z3's use the AUO panel.)

## linux-mdss-dsi-panel-driver-generator
[linux-mdss-dsi-panel-driver-generator] is a code generator that takes the downstream device tree as input,
and automatically produces clean Linux DRM panel drivers for each of the included panels. It was built based on the documentation below, so give it a try and use it as a base if you want to save some time. ;)

## Translating Mode Timings

DSI panels tend to support a single fixed resolution, described by `struct drm_display_mode`.  There are two common ways to represent the timings: `hdisplay`/`hsync_start`/`hsync_end`/`htotal` and `vdisplay`/`vsync_start`/`vsync_end`/`vtotal` (which drm uses), versus `width`/`h-front-porch`/`h-back-porch`/`h-pulse-width` and `height`/`v-front-porch`/`v-back-porch`/`v-pulse-width` (which downstream kernel uses).  Fortunately it is quite easy to convert between the two:

	hdisplay = width
	hsync_start = hdisplay + hfp;
	hsync_end = hsync_start + hpw;
	htotal = hsync_end + hbp;

	vdisplay = y_res;
	vsync_start = vdisplay + vfp;
	vsync_end = vsync_start + vpw;
	vtotal = vsync_end + vbp;

	clock = htotal * vtotal * frame_rate / 1000;

So:

	qcom,mdss-dsi-panel-framerate = <60>;
	qcom,mdss-dsi-panel-width = <1080>;
	qcom,mdss-dsi-panel-height = <1920>;
	qcom,mdss-dsi-h-front-porch = <56>;
	qcom,mdss-dsi-h-back-porch = <8>;
	qcom,mdss-dsi-h-pulse-width = <8>;
	qcom,mdss-dsi-h-sync-skew = <0>;
	qcom,mdss-dsi-v-back-porch = <8>;
	qcom,mdss-dsi-v-front-porch = <233>;
	qcom,mdss-dsi-v-pulse-width = <2>;

becomes:

	static const struct drm_display_mode default_mode = {
		.clock = 149506,
		.hdisplay = 1080,
		.hsync_start = 1080 + 56,
		.hsync_end = 1080 + 56 + 8,
		.htotal = 1080 + 56 + 8 + 8,
		.vdisplay = 1920,
		.vsync_start = 1920 + 233,
		.vsync_end = 1920 + 233 + 2,
		.vtotal = 1920 + 233 + 2 + 8,
		.vrefresh = 60,
	};

also, slightly related, the panel physical dimensions:

	somc,mdss-phy-size-mm = <64 114>;

becomes:

	panel->connector->display_info.width_mm = 64;
	panel->connector->display_info.height_mm = 114;

## Translating DSI Configuration

DSI has quite a number of different ways it can operate:
 * command mode (transmitting framebuffer data only when it changes) versus video mode (continuous vsync refresh, like a traditional desktop monitor)
 * dynamic changing between high speed and low speed operation versus not
 * various different # of lanes
 * etc

The upstream kernel has a number of `mode_flags` to configure the behaviour of the SoC's DSI driver so that it can talk properly to the panel.  In the downstream dts file, this is controlled by a number of different (sometimes optional) fields in the dts file.

 * `qcom,mdss-dsi-panel-type = "dsi_video_mode"` -> `MIPI_DSI_MODE_VIDEO`
   * no flag needed for `dsi_cmd_mode`
 * `qcom,mdss-dsi-h-sync-pulse = <1>` -> `MIPI_DSI_MODE_VIDEO_HSE`
 * not having `qcom,mdss-dsi-tx-eot-append` -> `MIPI_DSI_MODE_NO_EOT_PACKET` (note upstream flag disables, while downstream dts entry enables)
 * If panel uses both high-speed and low-power mode, set `MIPI_DSI_CLOCK_NON_CONTINUOUS`.  Check the `-command-state` fields for `dsi_lp_mode` and `dsi_hs_mode`.  If you see both, then set `MIPI_DSI_CLOCK_NON_CONTINUOUS` since we are switching dynamically between different speeds. There's a catch though - another field (`dsi-force-clk-lane-hs`) exists, which indicates that only high-speed mode is used. The issue is, some downstream kernels may use both of these fields, and even set them different to one another. In that case, dsi-force-clk-lane-hs takes precedence, and you can safely ignore the `-command-state` fields.

TODO probably more flags to document..

DSI can also operate in configurations with various # of lanes, pixel format, etc.  For example:

            qcom,mdss-dsi-lane-0-state;
            qcom,mdss-dsi-lane-1-state;
            qcom,mdss-dsi-lane-2-state;
            qcom,mdss-dsi-lane-3-state;
            qcom,mdss-dsi-bpp = <24>;

becomes:

	dsi->lanes = 4;
	dsi->format = MIPI_DSI_FMT_RGB888;


## Translating Programming Sequences:

The sequence of DSI messages to send is described in downstream kernel by the `-command` fields, for example the initialization sequence `somc,mdss-dsi-init-command`:

                somc,mdss-dsi-init-command = [15 01 00 00 00 00 02 FF E0
                                15 01 00 00 00 00 02 FB 01
                                15 01 00 00 00 00 02 B5 86
                                15 01 00 00 00 00 02 B6 77
                                15 01 00 00 00 00 02 B8 AD
                                15 01 00 00 00 00 02 FF 20
                                15 01 00 00 00 00 02 FB 01
                                15 01 00 00 00 00 02 10 04
                                15 01 00 00 00 00 02 FF 24
                                15 01 00 00 00 00 02 FB 01
                                15 01 00 00 00 00 02 C6 00
                                15 01 00 00 00 00 02 C5 32
                                15 01 00 00 00 00 02 92 92
                                15 01 00 00 00 00 02 FF 10
                                15 01 00 00 00 00 02 35 00
                                39 01 00 00 00 00 03 44 03 00
                                39 01 00 00 00 00 04 3B 03 30 06
                                15 01 00 00 01 00 02 BB 10
                                05 01 00 00 1E 00 01 11];

split out into table form to more easily see the different fields:

	                             P
	                             A
	  D                          Y
	  T  L        W      D       L
	  Y  A     A  A      L       O
	  P  S  V  C  I      E       A
	  E  T  C  K  T      N       D

	  15 01 00 00 00   00 02   FF E0
	  15 01 00 00 00   00 02   FB 01
	  15 01 00 00 00   00 02   B5 86
	  15 01 00 00 00   00 02   B6 77
	  15 01 00 00 00   00 02   B8 AD
	  15 01 00 00 00   00 02   FF 20
	  15 01 00 00 00   00 02   FB 01
	  15 01 00 00 00   00 02   10 04        ; MIPI_DCS_ENTER_SLEEP_MODE
	  15 01 00 00 00   00 02   FF 24
	  15 01 00 00 00   00 02   FB 01
	  15 01 00 00 00   00 02   C6 00
	  15 01 00 00 00   00 02   C5 32
	  15 01 00 00 00   00 02   92 92
	  15 01 00 00 00   00 02   FF 10
	  15 01 00 00 00   00 02   35 00        ; MIPI_DCS_SET_TEAR_ON
	  39 01 00 00 00   00 03   44 03 00     ; MIPI_DCS_SET_TEAR_SCANLINE
	  39 01 00 00 00   00 04   3B 03 30 06
	  15 01 00 00 01   00 02   BB 10
	  05 01 00 00 1E   00 01   11           ; MIPI_DCS_EXIT_SLEEP_MODE

Note that some messages have message id's defined by the MIPI-DSI spec (in comments above), others are panel specific.
Here is a list of what function a dtype hex is: http://elixir.free-electrons.com/linux/latest/source/include/video/mipi_display.h#L17

The above sequence becomes (with error handling omitted for brevity):

	dsi->mode_flags |= MIPI_DSI_MODE_LPM;
	ret = mipi_dsi_dcs_write(dsi, 0xff, (u8[]){ 0xe0 }, 1);
	ret = mipi_dsi_dcs_write(dsi, 0xfb, (u8[]){ 0x01 }, 1);
	ret = mipi_dsi_dcs_write(dsi, 0xb5, (u8[]){ 0x86 }, 1);
	ret = mipi_dsi_dcs_write(dsi, 0xb6, (u8[]){ 0x77 }, 1);
	ret = mipi_dsi_dcs_write(dsi, 0xb8, (u8[]){ 0xad }, 1);
	ret = mipi_dsi_dcs_write(dsi, 0xff, (u8[]){ 0x20 }, 1);
	ret = mipi_dsi_dcs_write(dsi, 0xfb, (u8[]){ 0x01 }, 1);
	ret = mipi_dsi_dcs_enter_sleep_mode(dsi);
	ret = mipi_dsi_dcs_write(dsi, 0xff, (u8[]){ 0x24 }, 1);
	ret = mipi_dsi_dcs_write(dsi, 0xfb, (u8[]){ 0x01 }, 1);
	ret = mipi_dsi_dcs_write(dsi, 0xc6, (u8[]){ 0x00 }, 1);
	ret = mipi_dsi_dcs_write(dsi, 0xc5, (u8[]){ 0x32 }, 1);
	ret = mipi_dsi_dcs_write(dsi, 0x92, (u8[]){ 0x92 }, 1);
	ret = mipi_dsi_dcs_write(dsi, 0xff, (u8[]){ 0x10 }, 1);
	ret = mipi_dsi_dcs_set_tear_on(dsi, MIPI_DSI_DCS_TEAR_MODE_VBLANK);
	ret = mipi_dsi_dcs_write(dsi, MIPI_DCS_SET_TEAR_SCANLINE,
			(u8[]){ 0x03, 0x00 }, 2);
	ret = mipi_dsi_dcs_write(dsi, 0x3b, (u8[]){ 0x03, 0x30, 0x06 }, 3);
	ret = mipi_dsi_dcs_write(dsi, 0xbb, (u8[]){ 0x10 }, 1);
	msleep(1);
	ret = mipi_dsi_dcs_exit_sleep_mode(dsi);
	msleep(30);

Note that we configure the dsi host for `MIPI_DSI_MODE_LPM` because `somc,mdss-dsi-init-command-state` is not `"dsi_hs_mode"`.  Also note that some messages have helper functions, like `mipi_dsi_dcs_enter_sleep_mode()`.

[linux-mdss-dsi-panel-driver-generator]: https://github.com/msm8916-mainline/linux-mdss-dsi-panel-driver-generator
