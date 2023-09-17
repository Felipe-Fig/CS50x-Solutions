# Practice Problem Link

https://cs50.harvard.edu/x/2023/problems/4/bottomup/

# Question:
## Why do image files need metadata?

Image metadata is text information pertaining to an image file that is embedded into the file or saved to a separate file that is associated with the image file.

Image metadata includes details relevant to the image itself and to its production. Some metadata is generated automatically by the system capturing or creating the image. Additional metadata can be added manually and edited through dedicated software or general image editing software, such as GIMP or Adobe Photoshop. Metadata can also be added directly on some digital cameras.

### Image metadata is often divided into three main categories:

- **Technical metadata** is mostly generated automatically by the device or software that creates the image. For example, if the image is a photograph taken by a digital camera, the camera typically generates metadata about the camera and the photo's settings, including aperture, resolution, focal length, shutter speed, ISO speed, camera brand and model, date and time when the image was created, and the GPS location where it was created. Cameras usually generate more technical data than devices or software such as scanners, screenshot tools or draw programs.

- **Descriptive metadata** is mostly added manually using special software such as GIMP or Affinity Photo. The individual who creates or manages the image can use the software to add or edit the descriptive metadata. The metadata might include the name of the image creator, keywords specific to the image, captions, titles, comments or other information. Effective descriptive metadata can make it easier to search for images.

- **Administrative metadata** is like descriptive metadata. Most of the metadata is added manually using special software. The metadata might include usage and licensing rights, restrictions on reuse, contact information for the image owner or similar types of information.

### Image metadata formats
Various standardized formats are used for metadata. The following four formats are the most common ones:

- **Exchangeable Image File (EXIF or Exif)**. The format is used extensively in digital cameras, smartphones and other devices when generating image metadata. EXIF data can include a range of information and often represents the bulk of metadata in an image file. It includes data such as image width and height, aperture value, exposure time, camera model and more.

- **Information Interchange Model (IIM)**. The metadata in IIM provides individuals and organizations with a way to add details to images such as titles, genres, instructions, owners or creators, location and contact information, copyright and attribution specifications, and similar types of information.

- **International Color Consortium (ICC)**. This metadata includes details about the color profile embedded in an image. ICC is an organization that defines and publishes open standards for image color management.

- **Extensible Metadata Platform (XMP)**. The metadata is an XML-based format that can accommodate a wide range of information. This format was created by Adobe but is now an ISO standard that has been adopted by Adobe and other vendors. XMP can incorporate both EXIF and IIM metadata.

Metadata is usually added to an image file directly, along with the bits that define the image itself.

# Extra
The bottomup.c file is "as-is" from the CS50x, just so you can see what was the problem. If you try to run it, you'll see that it compiles but it doesn't flips the image.